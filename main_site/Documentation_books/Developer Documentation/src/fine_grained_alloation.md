[undone] : These are rough notes on the Byte allocator. Compile them into good documentation



WE WILL BORROW CODE FOR THIS.... no shame in that.  
But we will first understand what that module does under the hood.  


The allocator maintains a linked list of memory blocks to keep track of free and allocated memory regions.

Let's go through the main parts of the code and understand how allocation works:

    AllocList Struct: This represents a block in the linked list. Each block has a flags_size field, which contains the size of the block and a flag indicating whether it is taken (allocated) or free.

    KMEM_HEAD: This is a pointer to the head of the linked list, which represents the start of the available memory space for allocations.

    KMEM_ALLOC: This variable keeps track of the total number of allocated pages for the kernel heap.

    KMEM_PAGE_TABLE: This is a pointer to a table used for on-demand page allocation (not implemented in the given code).

    init(): This function initializes the kernel memory allocator by allocating and zeroing a fixed number of pages (512 in this case) to be used for the kernel heap. The first AllocList is created at the top of this allocated memory, marking it as free and covering the entire available memory.

    kzmalloc(sz: usize) -> *mut u8: This function allocates memory, similar to kmalloc(sz: usize) -> *mut u8, but it also zeros out the allocated memory. It uses kmalloc internally to perform the allocation and then sets all bytes to zero using a loop.

    kmalloc(sz: usize) -> *mut u8: This is the main function for memory allocation. It takes the size in bytes as input and returns a pointer to the allocated memory block. The function searches through the linked list to find a free block that is large enough to accommodate the requested size. If a suitable block is found, it marks the block as taken and returns a pointer to the user space (address after the AllocList).

    kfree(ptr: *mut u8): This function frees a previously allocated memory block pointed to by ptr. It marks the corresponding AllocList as free and then attempts to coalesce adjacent free blocks to reduce fragmentation.

    coalesce(): This function tries to merge adjacent free blocks into a larger free block to reduce fragmentation.

    OsGlobalAlloc: This is an implementation of the GlobalAlloc trait from the core library. It enables the kernel to use Rust's standard allocator API, which is useful when interfacing with other Rust libraries that expect to use the global allocator.

    alloc_error(l: Layout) -> !: This is the error handler for memory allocation failures. It panics when the global allocator is unable to allocate the requested memory.


abstraction 
- treat all pages allocated as one contiguous space, filed with memory blocks called alloclists
- 

- AllocationList - 
- The descriptor in this case is the AllocList Structure. It has 64 bits. It describes the state and size of a block. A block is a contiguous set of bytes.  
- The first bit of the AllocList structure states whether the block is taken or free. (1 == taken)
- The rest of the 63 bits represent the size 
- the smallest unit that can be allocated is 8 bytes because we set the alignment order at 3. When you request 3 bytes, you will get 8 bytes in return.
- The allocStrust


Look at the kmem.rs code in ch5 


## Alloc in Rust [no_std]
The crate::alloc is not included by default. Modules like core and std are included by default in the prelude.  
To access the crate add it to the project as an external crate.
```rust
extern crate alloc
```

[undone] : which modules of the alloc crate are relevant? Why do we need them?  