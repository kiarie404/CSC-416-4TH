# theory_on_paging

references :
- https://os.phil-opp.com/paging-introduction/


Supervisor mode in riscv introduces a paging system that pages the physical memory.  
But before we discuss the paging system that is specific to RISCV, we need to understand paging theory by itself.   

## Virtual addresses and Phsical addresses
Before we discuss paging, let's first find out what virtual addresses and physical addresses are.   


A **Physical memory address** is the constant and full address of a physical space in memory. It is the address pf a physical space in memory. Each physical memory address is unique. And each physical address represents a single unique physical space.  

A **virtual memory address** is like a variable that contains the Physical memory address as its value. Just like in programming, variables are unique only in a certain scope. Different variables can contain the same value... the same way different virtual addresses can represent the same physical memory addresses.    

A virtual address can be translated to its equivalent physical address.  
We need virtual addresses so that we can manipulate memory just like how we manipulate variables.  
Virtual Addresses are a way of abstracting the physical memory. 


