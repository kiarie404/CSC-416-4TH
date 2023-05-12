I don't understand what the ABI is. Future James, save us.

So right now I just got this from Chatgpt... but I don't understand it : 
"
ABI stands for Application Binary Interface. It is a set of rules that define how software interacts with the operating system and hardware at the binary level. An ABI specifies things like data type sizes, calling conventions, register usage, and system call numbers, among other things. By providing a standard interface between software components, an ABI enables software written in different programming languages, compiled by different compilers, and running on different hardware and operating systems to interoperate.
"

For example the RISCV ABI specifies how system calls should be made : all that ... load syscall into a7, store arguments into a0-a5.  
Now if your OS uses this RISCV syscall calling convention, you could say that your OS uses the RISCV ABI. You cannot directly use the RISCV ABI  on a x86 machine. The RISC-V ABI is specific to the RISC-V architecture and specifies things like register usage, stack layout, and calling conventions that are specific to that architecture. Probably x86 does not have mepc and mret instruction mighrt not work as the RISCV assembly instruction.  

If you were to run an operating system or program that was compiled for the RISC-V architecture on an x86 CPU, you would need an emulator or virtual machine that can simulate the RISC-V instruction set and system calls. However, the resulting binary would not conform to the x86 ABI, which specifies a different set of rules for how programs interact with the system on x86 CPUs.

What is the Linux-ABI
Can we have a custom API that combines aspects of Linux ABI and RISCV  