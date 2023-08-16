# Processes

[]

Processes are the whole point of the operating system... we get a chance to execute independent set of instructions on top of one communal CPU and hardware.    
These independent set of instructions need to be managed on how they get to use the communal hardware.  
And as real life is; to manage something you need to have real time metadata about that thing. For example, to manage cars you need to at least know their make and models. To manage processes, we store their individual metadatas on a structure called - "process_metadata".   

[q] Why are we wrapping kernel functions as processes?      
[q] What is a process?  

[q] How are processes stored in secondary memory?     
[q] How are processes loaded into primary memory?   


[q] What is a standard library? 


## Process Metadata
This is the metadata about a process.   
We need this metadata so that we can regulate how the each process accesses the shared hardware.    

### 1. Process State
Helps us know who is using the CPU, who is fit to be next, who is not fit to be next.   
This info helps us know how the CPU is being shared

The states include :    
- currently_executing
- waiting_for_condition
- waiting_for_CPU_to_be free
- Dead/finished
- Not_yet_started

### 2. The trapframe containing the process' context
This info helps store the CPU registers. Both priviledged registers and unpriviledged registers.    
We store the context so that after an we can allow a process to continue from where it was before it was forced to stop using the CPU.  

Contents of the trapframe :
    // normal registers
    pub regs : [usize; 32],
    pub fregs: [usize; 32],
    // control status registers 
    pub satp : usize,  // 512
    pub mstatus : usize, // 520
    pub mepc : usize, // 528
    pub mie : usize, // 536
    pub mcause : usize, // 544
    pub mtval : usize, // 552
    Program counter

### 3. The Stack
Each process is stored as an elf file in memory.    
Each elf file in this case has a stack section.  
We store the address of this stack in the process' metadata  

This is optional


## Storing the Process
We need to figure out where to store.   
1. the process metadata
2. The process itself (the parts that are loadable to the RAM)
3. The program (loadable + unloadable elf sections)


THe Elf files have memory references in them. If we loaded such an elf file on bare metal, it would access physical addresses.  
But if we load that same file on top of an OS that has an MMU, by default, those memory references would be referecing virtual memory.  
And that right there is some good news, we can make it a rule that all linkers that are linking for our OS to always start referencing memory from a known virtual address, it makes things easier to program....

I mean, we can be assured that all elf files first memory address would always be at a known address : eg 0x2000_0000.  
This predictabilit is also how the firmware knows how to load bare metal applications.  
Key-word... repeat after me .... "predictability"   

In the spirit of predictability, we will make all of the processes begin at address 0x2000_0000 (virtual address).  

THis is like how we wrote the riscv bare-metal file, we made sure the elf file began at 0x8000_0000. Because the riscv loader expected us to.  