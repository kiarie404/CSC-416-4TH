# Implementation Notes

### Implementing the byte_allocator

### The Global Allocator

### Switching on the MMU
- We needed to re-write the Boot loader
- We divide the Kernel into 2.
  - kinit() 
  - kmain()
- Kinit 
  - initializes physical memory
  - iitializes the allocator
  - identity maps the kernel space
  - writes the SATP value in the SATP register
  - Initializes the trap frame and stores the base address in the mscratch and sscratch registers.
  - returns the satp value to kmain
- kmain
  - does the rest of the kernel work in supervisor mode.
  - However all exceptions and interrupts are handled in machine mode. This is expensive but simple. We shut off the medeleg and mideleg.

[bug] You cannot place comments in the booze.s file line 123  --> Solved : remove the coments
[bug] It was hard to maintain values across registers. For example, kinit stored the satp as a return value in register a0. But kmain read zero from that same register :
---> solved : Passing values across functions in assembly did not work. So I resorted to using rust static mutable variables that were shared between kinit and kmain.   

Another option was to embed kmain within kinit. And do that all in Rust code.   As for switching cpu modes and modifying the csrs, it could be done using inline assembly


### Identity mapping the Kernel
- Switching on the MMU will allow us to use the MMU while in supervisor mode.  
- The kernel code is memory_address specific. Meaning that the code will not work if we change up the addresses refered to in the code. For example, the 0x1000_0000 address referenced in the UART is an actual address, changing that in code will mess up the functionality.

- The kernel code working in Supervisor mode needs to reference the SAME address. ie 0x1000_0000.  
- To make sure the kernel code references the same addresses, we will map the virtual addresses to be similar to the physical adresses that were used. Forced Identity mapping


Each process gets its own Translation Table. Its own root table. Its own ID     
Before we switch to supervisor mode, we have to create a Translation table that the kernel will use while in superisor mode.  
And this table will have direct mappings.   

To help us, we will have a function that maps pages in a mass manner. Eg identity_map_pages (from: addressx, upto: address_y, root_table: address_z, with access_bits: x)


1. We export the memory_variables using the mem_export assembly file
2. We Map everything the kernel needs while maintaining the access flags
   1. The text section : RX
   2. RO Data Section  : R
   3. Data Section     : RW
   4. BSS section      : RW
   5. Kernel Stack     : RW
   6. The MMIO parts
      1. UART : RW
      2. PLIC : RW
      3. CLINT : RW
      4. MTIME & MTIMECMP : RW

Remember that you need to align each address before submitting the address to the mapper. The Mapper deals with valid page adresses only.   

The layout of the MMIO parts can be found at : https://github.com/qemu/qemu/blob/master/hw/riscv/virt.c#L78C1-L78C1  
The Layout of the CLINT and PLIC proved tricky, so I used the addresses used here : OS_Site topic 3.2 


#### Exception and Interrupt handling
- All handled in machine mode
- Trap Frame defined in rust
- Where is the trap frame stored
- Where is the trap stack stored? ---> Within the trap frame's memory.
- How big is the trap stack?     ---> 8 bytes

The trap stack in the trap frame is a pointer to an 8 byte physical memory within the trap-frames physical memory.  
```rust
#[repr(C)]   // fir the sake of predictability of memory position in assembly
#[derive(Clone, Copy)]
pub struct TrapFrame {
	pub regs:       [usize; 32], // 0 - 255    (256 bytes)
	pub fregs:      [usize; 32], // 256 - 511 - (256 bytes)
	pub satp:       usize,       // 512 - 519 - 8 bytes
	pub trap_stack: *mut u8,     // 520       - 8 bytes
	pub hartid:     usize,       // 528       - 8 bytes
}
```

We store the base address of the mut static Trapframe in the mscratch register.  
When a process is loaded into the CPU, the address of its trapframe gets loaded into the mscratch register.  


##### THe use of macros
In the trap.s assembly, we have used the assembly macros .  
Here is a snippet :  
```asm
.macro save_gp i, basereg=t6
	sd	x\i, ((\i)*REG_SIZE)(\basereg)
```


**The SD instruction**  
sd rs2, offset(rs1) :
Stores the eight bytes in register x[rs2] to memory at address x[rs1] + sign-extend(offset).   
For example :   
```asm
sd x0 10(x1)
```

So you may store an address at register x1, let's say 0x1000. Then the value in register x0 will be stored at memory address (0x1000 + 10)

I our case, we initially stored the address of the TrapFrame in the mscratch register. We the transfered that value to the t6 register.  

Now we need to store the value of each register into the trapframe. Now we work with offsets of the Trap_frame's base register.  

**Why Macros?**  
macros can help in reducing the amount of code to be written. In this case, if macros did not exist, we would have written
```asm
sd  x0, 0 * 8(t6)
sd  x1, 1 * 8(t6)
....
sd x25, 25* 8(t6)  // we are multiplying by 8 to account for the bytes. We want accurate offsets
```

But now we can use the macros. And it will be interprered this way :
"sd	x\i, ((\i)*REG_SIZE)(\basereg)"
 
This macro is used to save a general-purpose register (x0 to x31) onto the trap stack (or any other specified memory location).
It takes two parameters: i, which represents the index of the register to be saved (0 to 31), and basereg, which is an optional parameter with a default value of t6.   

The macro uses the sd (store double-word) instruction to store the value of the general-purpose register x\i (where \i is replaced with the value of i) into memory.    

The memory address is calculated as ((\i)*REG_SIZE)(\basereg), which multiplies the index i by the register size (REG_SIZE, which is 8 bytes in this code) and adds it to the value of the base register (\basereg, where \ escapes the basereg variable).  



**Wrapping unsafe ASM code in rust wrappers**
wrapped the Riscv Instructions in wrappers to make it easy to update the the CSRs without directly using unsafe code or having to write unsynched assembly code.  


**Handling Interrupts and exceptions**    
- declare known trapframe
- define the kernels trapframe... the kernel is also a large process
- store the kernel trap frame somewhere known ---> we are not taking the route of using assembly code again. It is fickle. We use rust functions that are synched
- Under asm_trap_vector
  - save context of all regular registers in the trapframe (integer + floats)
  - 
  - 
- Under rust_trap_vector



### Wrong Turns
1. Considering the user processes will NOT run in user-mode, there is no need to abstract kernel functions as processes whose start addresses point to Rust functions that are inaccessible to user functions. [design]
2. There is also no need to implement syscall call conventions. This is because user programs execute at kernel level. [design]
3. Now what bugs me is : "How will I make user programs loadable without forcing the kernel to restart?"[design]
   1. pre-define a linked list of program metadatas that the init function parses through.  
   2. When you download a program from the net, it gets stored in the file system but its metadata gets stored in the linked list of metadatas
   3. program_run now puts the metadata in the list and activates it to "waiting_for_CPU" state
   4. And there you have it.... no restarts
4. Build an installer?[design]
5. Which WASI syscalls should I use?[design]
6. 


