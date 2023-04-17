Reference [Philip_OS blog](https://os.phil-opp.com/freestanding-rust-binary/#linker-errors)

most errors involving the linker stem from the fact that The compiler and the linker assume that 
1. you are building for the host target
2. There is an underlying Operating system
3. The underlying operating system uses the C runtime 

So how do you cross-compile without linker errors? 
- We need to make sure the compiler and linker know that we are not targeting the host system. We tell it which system we are targeting.
- We may need to tell it that there is no operating system underneath
- We need to tell the linker that we are not depending on host-specific C runtime.


To achieve the above, we can :
1. Explicitly tell the compiler that we are compiling for a specific target that does not have an underlying OS. So the Linker knows that there is no underlying OS. And that there is no C runtime in the execution environment.