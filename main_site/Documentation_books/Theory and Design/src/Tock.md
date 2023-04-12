Tock is an Embedded Operating system. Tock takes into account that in as much as process isolation is an awesome method of implementing security, it has its flaws. 

Process Isolation is when the OS gives every program an illusion that the program has been alocated its own memory and CPU and other OS modules such as file_systems. (xv6 reference). Processes communicate with each  other using a method called Inter-process communication (IPC). An IPC can be implemented using methods such as Pipes, Sockets and Shared memory.

Pipes :
- a pipe is a data structure with : a read fie descriptor, a data buffer, a write file descriptor.
- It acts as a temporary variable to store data being exchanged between 2 processes.
- So one process that has access to the write file descriptor writes to the buffer of the pipe while the process that has a read descriptor reads from the buffer.

    Advantages of pipes:
        - It is simple to implement
        - It is easy to implement some form of security by defining an access list to the end-points of the pipe.
        - Communication using a pipe is unidirectional, this makes the protocols needed for this communication simple. Synchronization issues between two communicating process can be verified and rectified.
        - Pipes are memory efficient from the aspect that they use a circular buffer that typycally has a fixed size. No additional memory gets allocated to accomodate the communication between two processe( Insert [wikipedia GIF](https://en.wikipedia.org/wiki/File:Circular_Buffer_Animation.gif))
    
    Disadvantages of Pipes
        - Pipes are unidirectional. This is a disadvantage when the communication between the process was initially meant to be bidirectional.
        - Memory inefficient when multiple pipes get used at the same time