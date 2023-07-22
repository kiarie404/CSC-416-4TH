
Qemu --> Boot --> kinit --> kmain   
Kinit messes around with physical memory in Machine mode while kmain messes with virtual memory in Supervisor mode  
Kinit gets us to kmain.
under kinit we do not accept any interrupts : This allows us to setup our machine without any disturbance from other cores or the PLIC 
- we make mepc point to kinit
- we make the return address of kinit point to the asm funtion that will transition us to kmain ; kinit returns ()
- we call mret and jump into kinit rust

And what are we doing under kinit? [here](#kinit)

- when kinit returns, we are on the function to transition to kmain
- we set the mstatus register
  - set MPP to 01 (supervisor)
  - set Previous machine interrupt-enable bit is 1 (MPIE=1 [Enabled])
  - wet Previous interrupt-enable bit is 1 (SPIE=1 [Enabled]).
- setnputs : No inpu mtvec to mtrap_vector
- set mepc to kmain
- set which specific interrupts are allowed by setting the MIE register
  - 1 << 1    : Supervisor software interrupt enable (SSIE=1 [Enabled])
  - 1 << 5    : Supervisor timer interrupt enable (STIE=1 [Enabled])
  - 1 << 9    : Supervisor external interrupt enable (SEIE=1 [Enabled])
  - 0xaaa = MEIP/SEIP and MTIP/STIP and MSIP/SSIP
- set return register to point to Shutdown. If main returns () ... shutdown

## kinit
Before kinit make sure that all linker variables are accessible in Rust 
Make kinit global for the linker using "extern"

What does kinit do?
- initialize the UART system
  - declare a single instance of the UART object
  - initialize the UART instance (configure the UART registers for communication)
- Initialize the page allocator system
    - clear the page destriptors in the heap
    - define ALLOC_Start, where the Pages Start
- Initialize byte allocation system (Borrow this)
    - define the number of kernel pages (512 pages)
    - create a kernel heap by allocating those pages, and zeroing them out
    - set the first AllocStruct
- Allocate Space for the Root Page Table immediately after the kernel Heap
- Set up the values of the paging 
    - Get position of the root table that was created during kmem::init()
    - Identity Map all the known kernel addresses specified in the linker script
    - Map the Kernel Heap 
    - Map the UART
    - Map the PLIC
    - Map the CLINT
  - Define the SATP value
- Define the address of the TRAP_FRAME for our current HART. Each HART gets one TRAP_FRAME (it is global)
  - Store the address of the TRAP_FRAME in the mscratch register
  - Store the address of the TRAP_FRAME in the sscratch register
  - Map the TRAP_FRAME address for this HART
- initialize the page memory allocation system
- 