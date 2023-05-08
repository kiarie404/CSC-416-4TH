# Setting Up processes

we have 2 kinds of processes that we need to set up :
1. Kernel processes
2. User Processes

Each process has a process structure. A process structure is a data structure that represents the state and attributes of a process in an operating system. It typically contains information such as the process ID (PID), the program counter (PC), memory allocation details, and other resources associated with the process.

The process structure is used by the operating system code to manage and control the execution of processes on the system. It allows the operating system to keep track of the resources used by each process, schedule them for execution, and provide them with access to system resources such as I/O devices and memory.

Under the process structure, we have the following pieces of information :
1. The process ID
2. The process state
3. The program counter
4. The address to the process stack
5. The TrapFrame
6. The process data section


#### 1. The Process state
The process state indicates whether a process is currently executing, waiting for input/output (I/O), waiting for a resource, or in some other state.  
In our case, the process state will indicate whether the process is : Running, Waiting, Sleeping or Dead.

**A Running process** is a process that is currently getting executed by the CPU. To be precise, the program counter is referencing addresses found within the process text section.

**A Sleeping process** is a process that voluntarily stopped and is taking a timeout in order to wait for its turn to use the CPU.

**A waiting Process** is a process that has involuntarily stopped because a certain condition has not been met. For example an I/O operation.

**A dead process** is a process that is non_existent or it has finished executing and has been terminated by the operating system. The PCB for a terminated process is typically removed from memory. 


#### The Trap Frame
undone

#### The process stack
undone

#### Initializing a process
1. Pass a kernel function address to Process_constructor, let's call this address k_func
2. create an empty process structure
3. Fill the process structure with the following default values :
   - Navigate into the Trapframe and set all values within the trapframe to zero
   - make the address pointing to the stack to point to a null pointer
   - make program counter point to a null address
   - make the process state to be dead
   - make the pid point to an invalid address
4. Allocate a couple of pages 


