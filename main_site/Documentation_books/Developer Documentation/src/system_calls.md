

### The ecall instruction
In assembly code, the ecall instruction takes no arguments like how other functions do : For example :  
```riscv
li a7, 93   // as you can see the Load immediate instruction takes 2 arguments (a7 and 93)
ecall       // but ecall has no arguments
```

In accordance to the RISCV Calling convention, when a system call is made :
    - The code of the system call gets saved in the a7 register. eg the code for the exit system call is number 1. If you use the RISCv- system calling convention, you will have the luxury of having syscalls and their corresponding codes defined for you. You are free to tweak them or add your own syscall types and codes.
    - The arguments of the syscall gets stored in registers a0-a5.
    - If the arguments are more than six, the remaining arguments get stored in the stack.
    - The return result from the syscall get stored in the a0 register.

The ecall does not directly make the CPU to switch modes... but it gets used in the process of swiching CPU modes.  
The ecall however makes a software interrupt. The CPU receives the software interrupt. And since the interrupt handler is defined in either Supervisor mode or Machine mode, the CPU is forced to switch mode and elevate its priviledges to the appropriate level. And as usual after the interrupt handler has done its thing it *returns the CPU back to the normal state*


*returns the CPU back to the normal state* means that the interrupt handler calls the mret instriction... or the sret instruction or the uret instruction.  
When the interrupt handler is called, it firsts saves the context of the interrupted process. It saves all the CPU registers in a struct called a TrapFrame.  
The Program Counter register in the trapFrame contains the address of the next instruction that was supposed to get executed if the interrupt had not happened.  
The MEPC register or SPEC register or EPC register in the trapFrame contains the address of the instruction that caused the interrupt. In our case, it contains the address to ecall.  
It is up to the trap handler to make the MEPC/ EPC/ SEPC to point to the next appropriate instruction address because... 

**Because when we call mret/ sret/ uret....**
    - **the CPU copies the value of MEPC to the trapframe PC.**
    - The CPU gets restored to the previous priviledge status




- 