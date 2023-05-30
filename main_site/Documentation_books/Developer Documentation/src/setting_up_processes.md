# Setting Up processes

What are processes in this context? - a process is a program in action. It is program code and data that has been copied from the hard disk and put in the RAM in a format that the CPU can interact with.

we have 2 kinds of processes that we need to set up :
1. Kernel processes  --> kernel functions that get packaged and summoned as a process
2. User Processes    --> non_kernel code that gets loaded into RAM from the Hard disk

### A program in memory, A program in Action

So how does a program in memory look like?   Well, it depends on the implementation of the particular OS. In our case, all the memory associated with a process include :   


A. **The Process Structure** - The process structure contains the metadata about the process.    
B. **The process stack** - The process stack is where local variables and function addresses get temporarily stored at rutime.It is like an ordered buffer of instructions for the CPU to fetch.     
C. **The process *loadable* elf sections** - This is where the code and data of the program are defined. The stack gets its data from here.  
D. **The process trap_frame** - when the CPU performs a ontext switch, the context of the interrupted process gets stored in the Trap Frame





#### A. The Process Structure
Each process has a process structure. A process structure is a data structure that represents the state and attributes of a process in an operating system. A process structure contains metadata about a process.   
It typically contains information such as the process ID (PID), the program counter (PC), memory allocation details, and other resources associated with the process.

The process structure is used by the operating system code to manage and control the execution of processes on the system. It allows the operating system to keep track of the resources used by each process, schedule them for execution, and provide them with access to system resources such as I/O devices and memory.

Under the process structure, we have the following pieces of information :
1. The process ID
2. The process state
3. The program counter
4. The address to the process stack
5. The TrapFrame
6. The process data section

here is a rust presentation of the Process structure :
```rust
#[repr(C)]
pub struct Process {
  frame:           TrapFrame,
  stack:           *mut u8,
  program_counter: usize,
  pid:             u16,
  root:            *mut Table,
  state:           ProcessState,
}
```


#### 1. The Process state
The process state indicates whether a process is currently executing, waiting for input/output (I/O), waiting for a resource, or in some other state.  
In our case, the process state will indicate whether the process is : Running, Waiting, Sleeping or Dead.   
THis information is important because it will help us know :
- Which process to kill
- Which process to remove from the CPU
- Which process is ready to be loaded to the CPU.

**A Running process** is a process that is currently getting executed by the CPU. To be precise, the program counter is referencing addresses found within the process text section.

**A Sleeping process** is a process that voluntarily stopped and is taking a timeout in order to wait for its turn to use the CPU. This process will run perfectly if given a chance to get adressed by the CPU program counter.

**A waiting Process** is a process that has involuntarily stopped because a certain condition has not been met. For example an I/O operation.

**A dead process** is a process that is non_existent or it has finished executing and has been terminated by the operating system. The PCB for a terminated process is typically removed from memory. 


#### The Trap Frame
If the CPU gets an interrupt, it immediately switches to machine mode and calls the interrupt handler function found in the MTVEC register... the interrupt handler saves the context of the interrupted process in a structure called a Trapframe.   
Here is the structure of a trapframe :       
```rust

#[repr(C)]
#[derive(Clone, Copy)]
pub struct TrapFrame {
  pub regs:       [usize; 32], // 0 - 255
  pub fregs:      [usize; 32], // 256 - 511
  pub satp:       usize,       // 512 - 519
  pub trap_stack: *mut u8,     // 520
  pub hartid:     usize,       // 528
}

```

Each process has its own trapframe. This is so because there is a static queue of Process structures declared in the heap memory. And each Process structure contains a Trap Frame inside.  


#### Initializing a process
Creating a process is all about imprinting a program in the RAM. So our fisrt objectie would be to allocate free memory in the RAM in order to store the : Process Structure, Process Trap frame, Process Stack and Process elf sections. 

But there is already space for the Process structure in the Process_List Queue... so we just update one of the empty slots in this queue with the new proces information.  

The trap frame has been declared as part on the Process structure, so there is already some space for it.   

Since we are wrapping up a kernel process, we don't have elf sections, instead we will reference the function address as the entrypoint of the process.  

We allocate 2 pages for the process stack. We only store the pointer to the stack in the Process structure.

We are dealing with virtual addresses. And as you recall, each process is given a unique ID. And each ID is associated with a 

1. Pass a kernel function address to Process_constructor, let's call this address k_func
2. create an empty process structure
3. Fill the process structure with the following default values :
   - Navigate into the Trapframe and set all values within the trapframe to zero
   - make the address pointing to the stack to point to a null pointer
   - make program counter point to a null address
   - make the process state to be dead
   - make the pid point to an invalid address
4. Allocate a couple of pages 


