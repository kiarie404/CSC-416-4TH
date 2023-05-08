# Multitasking

main reference material : [Philip Opperman's blog](https://os.phil-opp.com/async-await/)

Programs share the CPU. There is an illusion that programs are executing in parallel. This illusion is achieved through multitasking.    
There are various multitasking techniques :
    - Preemptive multitasking
    - Time sliced mutltitasking
    - Co-operative multitasking

**Preemptive multitasking** - The kernel says : "Program A, you have used the CPU enough... I am cutting you off from the CPU... I will make program B have access to the CPU".

**Time sliced Multitasking** - Each process gets to use the CPU for a specified interval of time. The scheduler might to his priority sorting but fact remains that a process switch occurs after a specified time irregardless.

**Co-operative multitasking** - The CPU and the kernel don't decide the amount of time a program gets to use the CPU. THe program itself decides when to stop using the CPU and share it with others. It is expected that the programs are not selfish and that they care about each other... they co-operate with each other. Sounds like a fantasy.


Cooperative multitasking was commonly used in early operating systems because it was simpler to implement and required less overhead than preemptive multitasking, which requires the kernel to forcibly take control of the CPU from running tasks. However, as computer systems became more complex and demanding, preemptive multitasking became the norm because it offers better control over system resources and can improve system responsiveness.

That being said, cooperative multitasking can still be useful in certain situations, such as when dealing with low-level embedded systems or real-time applications, where predictability and determinism are more important than system performance. In these cases, cooperative multitasking can provide a simpler and more reliable way to manage system resources.

The Past is the future.... haha


A **coroutine** is a special kind of function that can pause its execution, save its current state, and resume execution from where it left off later, without losing its context or stack.

Cooperative multitasking is often used at the language level, like in the form of coroutines or async/await. The idea is that either the programmer or the compiler inserts yield operations into the program, which give up control of the CPU and allow other tasks to run. For example, a yield could be inserted after each iteration of a complex loop.

So we can implement asynchronous operations using co-operative multitasking. When a process needs to call an I/O operation that may take some time... we make it yield control to the CPU after calling that expensive I/O operation.

Since the programs themselves define their pause points, they are the one that know which information is important to save. it is up to the program to save its state status. This results in better performance. For example, a task that just finished a complex computation might only need to backup the final result of the computation since it does not need the intermediate results anymore.  

For example, the Rust Async/Wait struct automatically creates a struct that stores all variables that are still needed and the call stack values. Meaning you do not have to program the kernel to back up stack data. By backing up the relevant parts of the call stack before pausing, all tasks can share a single call stack, which results in much lower memory consumption per task.  

The thing about preemptive multitasking is that each task has its own stack. But in co-operative multitasking, all tasks can share one stack, which results in much lower memory consumption per task. This makes it possible to create an almost arbitrary number of cooperative tasks without running out of memory.






