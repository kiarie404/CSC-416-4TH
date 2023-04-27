In the previous section we did the following :
1. We abstracted the heap memory as pages that are associated to descriptors.
2. We provided the allocation and deallocation functions

In the previous module we were dealing with Physical addresses. We were executing code while the CPU was in Machine mode. This means that the memory management unit was turned off. Our code was referencing live physical addresses. eg memory_start = 0x8000000

In this module we want to abstract the physical RAM. We will virtualize all the memory addresses of the RAM. In real life the RISCV board provides a hardware implementation of a Memory management unit. The MMU can operate in Bare Mode, SV39 mode or SV48 mode.

Here is a theory discussion of the [theory on MMU implementation of Riscv](./theory_on_MMU_implementation_in_riscv.md).

As discussed in the theory :
1. For us to use the MMU hardware, we need to activate it, choose a mode and finally switch our cpu from machine mode to either Supervisor mode or Usermode.
2. We need to map all the linker_initialized memory locations.
3. When mapping the heap, we need to set aside kernel heap and user_program heap.  Isolating the two is good for security... and modularity. It means the kernel will always have a dedicated heap and that it will not compete for space with the rest of the user programs.
4. We need to implement an access control mechanism.
5. Each process should get a dedicated virtual address space

We will satisfy the above needs as follows :
#### Develop a Virtual MMU instead of using the physical MMU
We will not use the MMU hardware. Meaning that our kernel will continue executing in machine mode. Our user programs will also execute in machine mode. Instead of using the MMU hardware, we will implement a virtual MMU that works in SV39 mode.
Using the MMU hardware could have given us many advantages :
    Performance: Physical MMUs provide faster and more efficient memory access than virtual MMUs. This is because physical MMUs are implemented in hardware, which makes them faster than software-based virtual MMUs. The physical MMU has caches that make memory translation process much faster.

    Security: Physical MMUs provide better security than virtual MMUs. Physical MMUs can be used to implement hardware-based memory protection, which prevents unauthorized access to memory. This is not possible with virtual MMUs because they rely on software to implement memory protection.

    Reliability: Physical MMUs are more reliable than virtual MMUs. Since physical MMUs are implemented in hardware, they are less prone to software bugs and errors, which can cause system crashes or data corruption.

    Scalability: Physical MMUs are more scalable than virtual MMUs. As the size of physical memory increases, physical MMUs can be easily expanded to accommodate the increased memory, whereas virtual MMUs may require significant changes to the operating system and software.

With all this advantages, it is obvious that using the hardware MMU is the right choice. So why use a virtual MMU?  
Learning and understanding how the hardware MMU works in detail takes time. It will be faster to just understand how the MMU works from a high level and implement it in software form. Most of the details involved around understanding the hardware MMU are centered around space optimization. As a result, there is much bitmasking and predefined procedures to follow.  

For now, for the sake of implementation time, using the virtual MMU is the way to go.  
We will borrow SV39 mode concepts.

#### Major Tasks 
1. Abstract the SATP register
2. Abstract the root table, parent table and child tables
3. Abstract the entries
   - branch and leaf entries
4. Define functions to Map the Linker defined memory locations
5. Define functions to Map kernel heap addresses.
6. Define functions to Map user heap addresses
7. Define function to translate Linker defined memory locations
8. Define function to translate kernel heap addresses
9. Define function to translate user heap addresses
10. Define the API of the MMU
    - The exposed functions
    - The exposed structs
    - The success responses and error messages {this means you have to program }
  

#### Pseudocode
1. Abstract the SATP register
   