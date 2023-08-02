# Actual_implementation


- [x] get a grasp of core::mem and core::ptr [undone]
- [x] Implement Page allocator as a individual module
- [ ] Implement MMU as an individual module



MMU part :  
- Translator
- Contains translation Table
- Each process has a translation table{ root --> mid --> leaf}. Meaning each process gets a unique root table
- The ID of the process in relation to a root table address is stored by the kernel. 
- The ASID space in the SATP ensures that a process can only access physical memory that is found in the process' Translation table

- There are no 2 levels of virtual addresses like you thought. There is only one level.  
- THe root table can NET 512 GB of physical RAM
- Meaning that each process


I am still confused. Suppose the User program references the address : 0xFFFFFFFFFFFFFFFF. This address is 64 bits. And all bits are relevant. How can this virtual address be mapped to a 39 bit virtual address? Does this mean that a CPU that uses sv39 will  allow running of programs that only have virtual addresses that are within the 39 bit range?

" any user program running on such a CPU can only reference virtual addresses within this 39-bit range. In other words, the Sv39 MMU can handle 39-bit virtual addresses, and any address beyond that cannot be mapped using the Sv39 scheme."

[important] The use of sv39 MMU automatically forces you to ONLY run programs that contain virtual addresses that are within the 39 bit range. However, you can add physical extensions or virtual extensions.  
An example of a physical extension is using sv48 system.  

What is a program uses 64 bit virtual addresses?    
Why is a program eating 512 GB in the first place?  
Does it really need the 512 GB ram or it it using 64bit addresses for show/compatibility    
How do you port it :    
    - Convert 64 bit addresses to 39 bit
    - install as sv64
    - Add a virtual sv64 MMU on top of the sv39

Does this mean that we can instruct the linker to produce virtual addresses within a certain range?  
Does this mean that an elf loader needs to first check if the elf file references out of range addresses?   

The leaf Table Entries point to pages... not bytes  
Meaning that the 512 GB addresses can be multiplied by 4096. Than is how much physical RAM ca be represented using the sv39 MMU IF each page is 4096, only the 3rd table contains Leaf addresses, Leaf addresses represent pages only



Why shift by 2 places? 
    - Reasons : enforcing memory alignment requirements
    - a number that has 2 zeroes in the end means that it is a multiple of 4
    - When Some hardware strictly requires an address that is a multiple of 4, the hardware developers will force you to shift it by 2 so you notice if you are losing value. If you were submitting a number without 2 zeroes at the end, you will realize that you are making a mistake.
    - THe MMU hardware requires us to shift the address by 2 before storing the address in the Page Table Entries   
    - After shifting, that is when you can add the masks like Valid, U, R, X, W, ...
    - So when reading from the Table entry, remember to left shift by 2

#### SATP register
- THe root table address found in the SATP should be able to shrink to 44 bits
- The ASID should be read only
- Each process has a unique SATP because ASID is unique and Root_address is also unique.    
- Mode : 4 bits : 
- ASID : 16 bits : meaning that by default, you ca have 2^16 processes running at a time
- 

#### THe virtual address
"[9][9][9][offset]"  

#### Table Entry
- THe physical address part is 44 bits. It references whole pages... not bytes  
- It has some protection bits

#### the physical address
- [8 useless][56 bits used]  
- 56 bits part :  [26][9][9][Offset]

#### MMU errors
-  Page faults {data_store, data_read, istruction_fault}
-  Traslation errors
   -  long address than 39 bits
   -  Address unavailable for the process. Translation did not find a leaf Table Entry

#### API
- To the CPU
- To the PLIC


Functions :     
Assumptions : 
- You have got the physical page addresses from the page allocator.  
- You have virtual addresses that are divisible by 4096 too
- You have specifications {RWX} or throw an error
- The last table contains leaf addresses
- You have already verified the ASID
- You have already got the address of the Root Table (Physical address) from the SATP




1. Map (fills translation table)
2. Unmap (removes the entries of the table and frees all pages)
3. Translate (receives a virtual address and returns a physical address)

1. Map
   1. Inputs :
      1. Root Physical address extracted from SATP
      2. Valid Virtual address to a Page
         1. within the 39 bit range
         2. divisible by 4096... ie it is a valid page address
      3. Valid Physical address to a page
         1. Extracting from the Page allocator will guaratee validity
         2. within the 56 bit range
         3. divisible by 4096
      4. Valid access permissions {at least one specification to be provided}
         
   2. Errors : incorrect access specifications
   3. Process :
      1. 
- 
