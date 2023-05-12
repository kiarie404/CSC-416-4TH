# User Processes

#### How do we write userspace programs?

To write code for a user program, we need a standard library. The Std library contains general functions whose backends are connected to specific OS system calls or datastructures. For example the printf() function calls screen_output syscalls in the background.

We need to write our own Standard library.  
But how do we do that? Is it as complex as it sounds? I don't know ha ha ha wueeh! It's getting worse and more interesting... who knew?...I am so fucked. What was I thinking when choosing this project. Funny thing is, I don't care if I fail...meeh... what's going on.  

There are a couple of ways that we can run user programs... from now on let us assume that that user is hello_world.

We can make hello world part of the kernel. Such that if we modify the hello_worl, we have to recompile the whole OS. This means that the kernel will be like a crate and the system Interface will be the crate API. This is what people in embedded used to typically do, the kernel is just a library for the bare metal applications.

We will use this method before we write a standard library, write program loader and implement the file system. The second method below needs all the three things above to be completed

In the second method we compile the OS and load it in the RAM of a RISCV machine. We store a precompiled std library in the hard-disk. We store programs that depend on the std library on the hard disk too. Those programs get compiled while getting linked to the std library. 

we then load the program using the kernel's program loader