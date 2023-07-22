# The Bootloader  

For a long time I thought the way the CPU worked was some dark magic.  

The CPU is an indiscriminate machine, it is just a bunch of circuits that repeatedly do the operations below from the moment the CPU is powered on : 
- It reads the memory address storeed in the the program counter register. Let us call this memory address Address_X.
- Fetches the instruction pointed to by Address_X.
- Executes the instruction using one or more of its circuits/gates
- repeats infinitely

It is up to us to occasionally make the program counter to point to the instrutions we want executed.  
To control this machine, we have to change the value in its control status registers.  
When you look at it from this perspective, it no longer seems like dark magic.  
It is a machine that can be controlled by modifying the value in relevant registers.  

Each CPU comes with its own assembly language.   

We will be using RISCV assembly to write the assembly code for the bootloader.  
The definition of what a bootloader is supposed to do varies from OS to OS. For example, some bootloaders can perform hardware power_on tests while others just leave that work to the CPU firmware. 

A typical bootloader essentially does the following operations:
1. Find the memory address of the Kernel program by searching through the external memory devices that have been plugged into the Motherboard.
2. Loads the Kernel image onto the RAM. Note that it only loads the required sections, it might not load the entire image.
3. Prepare the values found in the CPU registers to suit the execution of the kernel. For example making the Stack pointer to point to the stack of the kernel. 
4. Transfering control to the kernel. This is done by making the program counter point to the entry_point of the kernel

The Bootloader in this chapter is much simpler. It does not have to look for the kernel image in a secondary memory like a hard-disk, instead, the Kernel and the bootloader are one program. 
The Basic Bootloader in this case is a software program that :
    - Finds the memory location of the kernel's entry_point (the first kernel instruction). This instruction address is still part of the bootloader. Remember that (Kernel + bootloader) == 1 united program
    - Loads the kernel image to memory
    - Transfers control to the kernel

The Bootloader has many definitions depending on the additional functionalities it has :
    - Dealing with the convertion of CPI from real mode to protected mode and finally to 64-bit mode
    - Having a user interface that gives its users the option to choose among multiple kernel images
    - Having the bootloader code implemented in seperate parts.  
    - Having the bootloader do some tasks that were originally meant for the firmware eg. Power_on_self tests
 We do not care about the above functionalities... for now.


### The Boot process
References : 
- [Booting in x86 CPU](https://os.phil-opp.com/minimal-rust-kernel/#the-boot-process)
- [Booting In RISCV](https://osblog.stephenmarz.com/ch1.html)

When the machine is powered on : 

#### The Firmware
The CPU begins its fetch-execute cycle. Typically, the PC register of the CPU points to a memory address in the ROM.  
The ROM contains hardcoded firmware code. In x86 this firmware can be BIOS or UEFI. 
The firmware code performs Power-on-tests on all pluged devices.   
The firmware code initializes the hardware : for example it maps the dedicated I/O MMIO memory and enumerates the RAM.  
After setting up the execution environment, it scans the plugged in secondary memory devices... Depending on the partitioning scheme used in those devices, it looks for valid bootloaders. At this point some firmware give the user a chance to choose which bootloader they would prefer among the ones the firmware has discovered

#### The Bootloader
After the Firmware has set up the execution environment, It makes the CPU pointer ton point at the entr point of the boot_code.  
In our case, the bootloader will do the following functions :
1.  Pick only one CPU to complete the execution of the bootloader code. This is because at the beginning we do not want any kind of paralellism. Moreover, the bootcode is a simple code that can easily be done by one CPU, adding parallelism increases unnecessary complexity. It means we will have to do interprocessor communications at the start. That is just unnecessary overengineering to save a nano_secog of a nanosecond of a nanosecond
2. Clear the uninitialized memory sections : the heap and BSS section.
3. Transfer control to the Kernel code found in memory

## Bootloader pseudo code
algorithm inputs : No inputs   
algorithm outputs : No outputs, it just calls another function that never returns.

1. set the necessary assembler directives
      - notify the assembler that the code should not use compressed code
      - define the memory sections
       - .text.init section : the .text.init section is different from other .text sections because the .text.init section contains initialization code that gets executed before the main function and other init sections. (.text.init) section must be executed before all other sections
       - .data section

2. Choose HART 0 as the main and only core that should continue to execute the boot_code
     - if the Hart ID is not 0, subject that core to an endless sleep

3. Confirm that the HART is in machine mode
4. Clear the BSS section, we need no surprises.  
5. Set up the regiters of the CPU to be ready to execute the kernel code
6. Call mret
   1. Set up some general registers
      1. fetch the global pointer so that we ge to access the data sections more confidently, update the global_pointer register
      2. update the stack pointer to point to the bottom of the kernel stack
      3. update the return address to point to an Endless sleep function... the kernel should not return in the first place
   
   2. Set up the control registers
      1. the mstatus register 
         1. set previous MPP to Machine mode, this is because we intend to run the kernel in Machine mode for some time before moving to S-Mode. When we call mret instruction, the CPU will be run in the mode specified in MPP
         2. set MIE mstatus bit to 1, this enables the CPU to catch interrupts
         3. set MPIE mstatis bit to 1, this enables the kernel to be able to receive interrupts once we transfer cotrol to it using mret.
      2. The machine_interrupt_enable register
         1. enable all the interrupts (Software, Timer and external)
      3. the machine_trap_vector 
         1. Let the mtvec register point to a trap vector point... defined globally in assembly language
      4. the MEPC should point to the kmain function
   - 
- Setup the CPU status to suit the jump to kernel code :
  - set the medeleg register  : we will not delegate any exception, we will handle all exceptions in Machine mode
  - set the mideleg register  : we will not delegate any interrupt to lower levels, we will handle all interrupts in Machine mode
  - set the mstatus to allow software interrupts and external interrupts
  - Set the MIE register to handle only 
  - set the stack pointer to point at the bottom of the stack
  - set the mstatus register :
    - allow interrups to be allowed in both machine mode and supervisor mode by setting the MPP(Machine Previous Protection) to the value 3 
  - set the MEPC ; machine exception program Counter to point to the kernel entry point.
  - Set the MTVEC : Machine Trao Vector to point to the Exception handling code
- Call MRET

**Why are we calling WFI?**  
We are calling the WFI instruction to put all the other CPU cores to sleep. Our OS only uses one HART (Cpu).  

The WFI (Wait for interrupt instruction) - This RISCV instruction powers off the CPU and only leaves a small circiut running. THis circuit continuously checks if an interrupt signal has been sent to the powered off CPU. If an interrupt is detected, the CPU gets powered on.  It is kind of a 'sleep' instruction. It can be used to save power when the CPU is idle.
We are calling the WFI instruction to put all the other CPU cores to sleep. Our OS only uses one HART (Cpu).


**Why are we disabling Riscv Compressed instruction?**
- So that we gain simplicity in debugging. 
  
In RISCV, the assembler usually encodes each assembly instruction into 32 bits. But this is not always the case, you can instruct the assembler to use compressed instructions. Compressed instructions are only 16 bits long. Not all assembly instructions get encoded to 16 bits... just a select few. This ensures memory efficient code.  
However, it makes it hard to debug code because the not ALL instructions are 32 bits as before.  

we achieve this by using either of the two assembly directives : 
```riscv
.option norvc      // No RiscV compressed instructions
.option rvc        // Yes to RiscV compressed instructions
```

**Why do we need to load the global pointer when writing the Bootloader?**
So that the bootloader code gets to use the correct global data associated to the Operating system image.  

The global pointer references the base address of global data in the memory map.  
To access any global data, you have to know the base address + offset.  
The bootloader typically gets executed as a seperate program from the operating system. So the bootloader may have a different memory map from the memory map of the kernel.  
Considering that the bootloader needs to use the operating system's global data, we make the global pointer that the bootloader references to be the gp found in the memory map of the kernel.  

Now this operation is delicate; we are accessing another memory map to access global data that may or may not be the same as the data we are trying to change. We need to make this operation explicit, no surprises. So we temporarily kill all code optimizations when doing this operation by using the directives :   

```riscv
.option push       // save previous assembly directives... because in the next few lines we may use contradicting directives
.option norelax    // no optimization
.option pop        // restore previous assembly directives
```

## Designs

#### Initial Program Flow diagram
<body>
  <pre class="mermaid">
        graph TD 
        A[Qemu ELF loader] -->|loads Elf file containing kernel loader| B[kernel Loader] 
        B --> |prepares the CPU registers for kernel, calls kernel entry point| C[Kernel Runs] 
  </pre>
</body>



#### Kernel loader sequence of events
```mermaid
    graph TD 
        A[set assembler directives] --> B[Look for HART 0] ;
        B --> C{Is the Core HART 0?} ;
        C -->|Yes| D[Clear Kernels BSS section];
        C -->|No| E[Put HART to sleep];
        D --> F[initialize CPU registers for kernel];
        F --> G[summon kmain];
```
