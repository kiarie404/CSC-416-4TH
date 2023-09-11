Roadmap  
- [x] Build the Bootloader
- [x] Write the UART driver to integrate with the Keyboard and Console
- [x] Establish Page-grained memory allocation
- [x] Establish Byte-grained memory allocation
- [x] Build Trap Handling (Internal and External)
  - [x] Timer Interrupts
  - [x] Software Interrupts
  - [x] Internal Interrupts 
  - [x] Environment calls, breakpoint
  - [ ] The rest of the RISCV specified exceptions

- [x] Build I/O (stdin and stdout) to work in an interrupt-driven fashion
- [x] Add a Block driver to interact with the virtual Hard-disk
- [x] Integrate the Minix 3 File system

- [x] Abstract kernel core functions as processes, build a scheduler
- [ ] Integrate A wasm runtime (wasmi) as part of the kernel
- [ ] Expose the kernel processes using wasi prototypes
- [ ] Write a minimal std library for the user programs to use'
- [ ] Build a user program loader (improve the scheduler)
- [ ] Package the kernel as an ISO file


