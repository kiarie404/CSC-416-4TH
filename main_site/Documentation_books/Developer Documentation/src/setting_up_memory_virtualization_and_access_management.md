In the previous section we did the following :
1. We abstracted the heap memory as pages that are associated to descriptors.
2. We provided the allocation and deallocation functions

In the previous module we were dealing with Physical addresses. We were executing code while the CPU was in Machine mode. This means that the memory management unit was turned off. Our code was referencing live physical addresses. eg memory_start = 0x8000000

In this module we want to abstract the physical RAM. We will virtualize all the memory addresses of the RAM. In real life the RISCV board provides a hardware implementation of a Memory management unit. So this unit provides a way to create virtual addresses and a way to declare access rights to those addresses. The MMU can operate in Bare Mode, SV39 mode or SV48 mode.

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
1. Performance: Physical MMUs provide faster and more efficient memory access than virtual MMUs. This is because physical MMUs are implemented in hardware, which makes them faster than software-based virtual MMUs. The physical MMU has caches that make memory translation process much faster.

2. Security: Physical MMUs provide better security than virtual MMUs. Physical MMUs can be used to implement hardware-based memory protection, which prevents unauthorized access to memory. This is not possible with virtual MMUs because they rely on software to implement memory protection.

3. Reliability: Physical MMUs are more reliable than virtual MMUs. Since physical MMUs are implemented in hardware, they are less prone to software bugs and errors, which can cause system crashes or data corruption.

4. Scalability: Physical MMUs are more scalable than virtual MMUs. As the size of physical memory increases, physical MMUs can be easily expanded to accommodate the increased memory, whereas virtual MMUs may require significant changes to the operating system and software.

With all this advantages, it is obvious that using the hardware MMU is the right choice. So why use a virtual MMU?  
Learning and understanding how the hardware MMU works in detail takes time. It will be faster to just understand how the MMU works from a high level and implement it in software form. Most of the details involved around understanding the hardware MMU are centered around space optimization. As a result, there is much bitmasking and predefined procedures to follow.  

For now, for the sake of implementation time, using the virtual MMU is the way to go.  
We will borrow SV39 mode concepts.

#### Major Tasks 
1. Abstract the SATP register; This will help us identify : The process address space
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
  

### Quick Detour....
We will not implement the sv39 emulation due to time constraints. Such a disappointment. All that reading for nothing.  We will leave that for future Implementations.  
We will implement a one to one mapping. Something like this : 

**The Translation Table.**

| Process_with_access_rights | Access Parameter | Virtual memory                 | Physical Memory |
|----------------------------|------------------|--------------------------------|-----------------|
| 0(kernel)                  | R/E              | 0x80000000(text_section)       | 0x80000000      |
| 0(kernel)                  | R/W/E            | 0X80002000(stack_end)          | 0X80002000      |
| 0(kernel)                  | R/W/E            | 0x80005000 (Kernel_heap_start) | 0x80005000      |
| 0(kernel)                  |                  |                                |                 |
| 1(init)                    |                  |                                |                 |
| 1(init)                    |                  |                                |                 |


Our system is not fine_grained, meaning that to access a specific page, you have to first find the first allocated page for that associated with the contiguous block.  
Do not Let the transalation Table fool you, the Kernel process has first class access rights to all other processes. THis is dangerous, it will be fixed in the future [undone] 

Kernel virtual addresses are mapped using a different mechanism from the one used to map user processes.  
Mapping is the process of populating an atomic entry in the the Translation table.  

#### The Mapping procedure (For processes that are not Kernel related):   
- The OS decides to create space for a user process.    
- The OS realizes that it has to create space for each of the following process elements: 
  - The different LOAD-ABLE sections of the user process elf_file
  - The stack
  - The process structure 
- It calls the allocator to help allocate pages for each of the above sections. The Elf sections should fall under a contiguous memory space. The allocator returns the physical start addresses for each element.
- Now the kernel has the physical memories it can use to populate part of the Translation Table. The Translation table now looks as follows :     

| Process_with_access_rights | Access Parameter | Virtual memory | Physical Memory                          |
|----------------------------|------------------|----------------|------------------------------------------|
|                            |                  |                | 0xsome_address (text_section)            |
|                            |                  |                | 0xsome_address (data_section)            |
|                            |                  |                | 0xsome_address (bss_section)             |
|                            |                  |                | 0xsome_address (other_loadable_sections) |
|                            |                  |                | 0xsome_address (Stack_start)             |
|                            |                  |                | 0xsome_address (Process Structure)       |



- The kernel also has information about which processes have access to the allocated sections. ie 
  - The elf sections belong to the user process. Only the kernel and the user process have access to these sections
  - The stack belongs to the user process. Only the kernel and the user process have access to these sections.
  - The Process Struture belongs to the Kernel.
- So our Mapper function updates the table as follows :     


| Process_with_access_rights | Access Parameter | Virtual memory | Physical Memory                          |
|----------------------------|------------------|----------------|------------------------------------------|
| 1(hello_world)             |                  |                | 0xsome_address (text_section)            |
| 1(hello_world)             |                  |                | 0xsome_address (data_section)            |
| 1(hello_world)             |                  |                | 0xsome_address (bss_section)             |
| 1(hello_world)             |                  |                | 0xsome_address (other_loadable_sections) |
| 1(hello_world)             |                  |                | 0xsome_address (Stack_start)             |
| 0(kernel)                  |                  |                | 0xsome_address (Process Structure)       |


- Now using an Elf reader, you extract info about the R/W/exeute access of each section of the elf file and update the relevant info in the Translation Table : 
| Process_with_access_rights | Access Parameter | Virtual memory | Physical Memory                          |
|----------------------------|------------------|----------------|------------------------------------------|
| 1(hello_world)             | R/E              |                | 0xsome_address (text_section)            |
| 1(hello_world)             | R/W              |                | 0xsome_address (data_section)            |
| 1(hello_world)             | R/W              |                | 0xsome_address (bss_section)             |
| 1(hello_world)             | applicable_right |                | 0xsome_address (other_loadable_sections) |
| 1(hello_world)             |                  |                | 0xsome_address (Stack_start)             |
| 0(kernel)                  |                  |                | 0xsome_address (Process Structure)       |


- The stack is a read_write... is it? I really don't know.  The Process structure is also a read_write : 

| Process_with_access_rights | Access Parameter | Virtual memory | Physical Memory                          |
|----------------------------|------------------|----------------|------------------------------------------|
| 1(hello_world)             | R/E              |                | 0xsome_address (text_section)            |
| 1(hello_world)             | R/W              |                | 0xsome_address (data_section)            |
| 1(hello_world)             | R/W              |                | 0xsome_address (bss_section)             |
| 1(hello_world)             | applicable_right |                | 0xsome_address (other_loadable_sections) |
| 1(hello_world)             | R/W              |                | 0xsome_address (Stack_start)             |
| 0(kernel)                  | R/W              |                | 0xsome_address (Process Structure)       |

- now we are left with the virtual memory coulumn. Each Virtual memory address belonging to a particular process needs to be unique when compared to other virtual addresses assigned to the same process. However, virtual memory addresses belonging to different processes can be similar because they are under different processes.

- The Virtual addresses for the different elf sections can be extracted from the elf file using the elf_reader. So the Translation Table becomes :  
| Process_with_access_rights | Access Parameter | Virtual memory              | Physical Memory                          |
|----------------------------|------------------|-----------------------------|------------------------------------------|
| 1(hello_world)             | R/E              | elf text_section            | 0xsome_address (text_section)            |
| 1(hello_world)             | R/W              | elf data_section            | 0xsome_address (data_section)            |
| 1(hello_world)             | R/W              | elf bss_section             | 0xsome_address (bss_section)             |
| 1(hello_world)             | applicable_right | elf other_loadable_sections | 0xsome_address (other_loadable_sections) |
| 1(hello_world)             | R/W              |                             | 0xsome_address (Stack_start)             |
| 0(kernel)                  | R/W              |                             | 0xsome_address (Process Structure)       |

- If the elf file specifies the virtual address of the stack... use it.
- If the elf file does not specify the virtual address for the Stack, you assign it a virtual address that does not coincide with any of the elf virtual addresses. To make sure of this, you pick a virtual address that comes right after the end of the virtual addresses specified in the elf file. The chosen address needs to be aligned to a multiple of 4096.

So the Translation Table now is updated to this :   
| Process_with_access_rights | Access Parameter | Virtual memory                  | Physical Memory                          |
|----------------------------|------------------|---------------------------------|------------------------------------------|
| 1(hello_world)             | R/E              | elf text_section                | 0xsome_address (text_section)            |
| 1(hello_world)             | R/W              | elf data_section                | 0xsome_address (data_section)            |
| 1(hello_world)             | R/W              | elf bss_section                 | 0xsome_address (bss_section)             |
| 1(hello_world)             | applicable_right | elf other_loadable_sections     | 0xsome_address (other_loadable_sections) |
| 1(hello_world)             | R/W              | (elf stack) OR (after_elf addr) | 0xsome_address (Stack_start)             |
| 0(kernel)                  | R/W              | 0xsome_address_X                | 0xsome_address_X (Process Structure)     |

- Now what remains is the Kernel virtual address. Drum rolls.... Boom! : The Kernel does not use virtual addresses that are different from the physical addresses. Kernel virtual addresses are straight foward : a one-to-one mapping. This decision is NOT a bad decision. This is because you can treat the Kernel virtual addresses as normal virtual addresses. for example, multiple virtual addresses can point to a single physical address.


#### The Translation Process

When a process wants to access the RAM, it uses virtual addresses.  The instructions 
