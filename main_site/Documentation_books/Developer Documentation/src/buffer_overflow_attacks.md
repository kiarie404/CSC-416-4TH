# buffer_overflow_attacks

References : 
    - [How to conduct an Buffer_overflow attack](https://samsclass.info/127/proj/p3-lbuf1.htm)

Buffer overflow is a type of vulnerability that occurs when a program writes data beyond the end of a buffer or allocated memory region, potentially overwriting other data or code. This type of vulnerability can be exploited by attackers to execute arbitrary code or modify program behavior in unintended ways. Buffer overflow attacks are a common type of security vulnerability, and have been used in many high-profile attacks over the years.

The basic idea behind a buffer overflow attack is to supply more data than a buffer can hold, causing the excess data to overwrite adjacent memory locations. For example, consider a program that reads user input into a fixed-size buffer:

```C
char buffer[10];
gets(buffer);
```

If a user supplies more than 10 characters of input, the excess characters will be written beyond the end of the buffer, potentially overwriting other memory locations. An attacker can use this vulnerability to overwrite critical program data or code with their own malicious code, which the program will then execute.

There are several techniques that attackers can use to exploit buffer overflow vulnerabilities, including:

    Code injection: An attacker can inject their own code into the program's memory space by overwriting a function pointer or return address. When the program returns to the overwritten code, it will execute the attacker's code instead of the original code.

    Denial of service: An attacker can cause a program to crash or hang by overwriting critical data structures or interrupt handlers. This can be used to disrupt the normal operation of a system or to prevent the program from detecting other attacks.

    Privilege escalation: An attacker can use a buffer overflow vulnerability to gain elevated privileges on a system by overwriting a memory location that controls access control or other security-related features.

To prevent buffer overflow attacks, programmers must be careful to properly manage buffer sizes and ensure that all memory writes are properly bounds-checked. Many programming languages and frameworks provide built-in protections against buffer overflow attacks, such as array bounds checking, stack canaries, and address space layout randomization (ASLR). Additionally, secure coding practices, such as input validation and output encoding, can help prevent buffer overflow attacks by limiting the amount and type of data that a program accepts from external sources.



Practical Example : 
- use unsafe language to write program
- use an intentional hack () 


### ME

Buffer overflow attacks are possible because the attacker knows the layout of the program in memory. This enables them to know where to ovewrite. As a solution, operating systems implement something called "Address space layout randomization".  

Under Address space layout randomization, the operating system randomizes the memory positions of key units in the layout of the memory of the process. For example the stack, heap, text section.  

The problem now becomes that randomizing the memory positions will cause the program to crash. This is because the program counter will stil point to the memory addresses that it was meant to point to before randomization happened. 

To solve this Program counter problem. The ASLR gets executed in a predictable manner, such that the PC can be updated by adding an offset. Since the ASLR is predictable... it means hackers only neet to predict the offset that the PC is being fed. A security measure need not be predictable.

A solution to this 'predictability' problem of the ASLR gets partially solved by : "running the ASLR algorithm each time the program is executed" - This means that the attacker can only start predicting the memory layout after a program gets executed. This does not mean the program is safe. Once the attacker figures out the ASLR pattern... it's game over.

The ASLR partially solves the Buffer overflow problem. It just makes it hard for the attacker to understand the memory layout of a program. It makes it hard for someone to find an opprtunity to execute an attack. But it does not make the program safe from the actual attack.

The ASLR also does not typically randomize kernel memory regions. (I have no proof, I read from a blog ___ Look_into_this). THis leaves the kernel vulnerable

ASLR in Linux typically does not randomize kernel memory regions. This leaves the kernel vulnerable to certain types of attacks, such as kernel buffer overflow exploits, which can be used to gain unauthorized access to the system.

The reason for this is that the kernel is responsible for managing the memory of the entire system, and it needs to have a predictable layout of kernel memory regions in order to function properly. Randomizing the location of kernel memory regions could potentially break system functionality and cause stability issues.

Instead of relying solely on ASLR, the Linux kernel employs a variety of other security mechanisms to protect against attacks on kernel memory, such as kernel address space layout randomization (KASLR), which randomizes the location of the kernel image in memory. Other security mechanisms include memory protection and access controls, input validation, and privilege separation, among others


### Experiment

The call stack is a region of memory that is used to manage the execution of function calls in a program.  
Each program has a call stack.  
When a function is called, a new stack frame is created and pushed onto the call stack, and when the function returns, its stack frame is popped off the stack.  


The Extended Stack Pointer (ESP) is a register in the x86 computer architecture that is used to keep track of the memory location of the current stack frame.  
So if you read the ESP, you will be getting a value within the call stack of the current executing thread in CPU.  


As easlier discussed, Linux uses ASLR to protect code and data found in the User Address Space. 
The "randomize_va_space" file in the "/proc/sys/kernel" directory is used to control the behavior of ASLR in the Linux OS.  
You can configure the ASLR mode using one of the following commands :  
```bash
echo 0 > /proc/sys/kernel/randomize_va_space   // Disable ASLR completely
echo 1 > /proc/sys/kernel/randomize_va_space   // affect callstack only 
echo 2 > /proc/sys/kernel/randomize_va_space   // affect all memory regions
```

The "proc" file system allows users and applications to retrieve information about system configuration, hardware devices, running processes, and other system-level data. For example, the /proc/cpuinfo file provides detailed information about the processor(s) installed in the system, such as their model, speed, cache size, and features.  

Setting the config value to 1 enables ASLR capabilities on the user_program stack and mmap-based allocations only. It disables ASLR on those other types of memory such as heap, shared libraries.  

Setting the config value to 2 enables ASLR capabilities on all the user_program memory sections. This is full ASLR.  The config value 2 is usually the default.  

To execute our BF attak, let as assume that we have cracked the ASLR. So let's dissable the ASLR by stiing the value in randomize_va_space file to 0.  

Note to self :
    Under C we usually have stuff like this : "void main(int argc, char *argv[]) "

    The "argc" parameter is an integer that represents the number of command-line arguments passed to the program, including the program name itself. For example, if you execute a program named "myprog" with the command "myprog arg1 arg2 arg3", argc would be 4.

    The "argv" parameter is an array of pointers to strings, where each string represents a command-line argument passed to the program. The first element of the "argv" array is a pointer to the program name, and the remaining elements are pointers to the strings representing the other command-line arguments.

So we are going to do the attack in two fronts :
    - Cause a Denial of Service by crashing a program.
    - Execute bad code 

So we write a program that overloads the buffer of an array. So it causes a segmentation Fault. A segmentation fault, often called a segfault, is a common error that occurs when a program attempts to access memory that "does not belong to it." This typically happens when a program tries to read or write to memory that it doesn't have permission to access, or when the program tries to access memory that simply does not exist.

When a program causes a segmentation fault, it is usually terminated by the operating system to prevent further damage to the system. The error message for a segmentation fault often includes the phrase "core dumped," which means that a snapshot of the program's memory has been saved to disk for debugging purposes.

Now, we have managed to crush a user program... can we crush the OS ?


