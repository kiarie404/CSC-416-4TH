# The Global Allocator

Each operating system has an allocation system. An allocation system goes through the heap memory and returns the free memory that is was requested. At times, it is also used to deallocate memory fro the heap.

Each operating system has its own way of dealing with the heap. Meaning that Oses have different allocator implementations.     
Data structures like vectors and linked lists are built on the heap. This means that for you to create a linked list, you have to have a heap allocator... and heap allocators are OS_dependent.    
For this reason data structures that depend on the heap are not available by default in a NO_STD environment. We need a way to make the compiler know that we have writen our own heap allocator and that it can use this heap allocator to implement data structures like the heap.

References :
- [Readable documentation](https://web.mit.edu/rust-lang_v1.25/arch/amd64_ubuntu1404/share/doc/rust/html/unstable-book/language-features/global-allocator.html)
- [Official crate documentation](https://doc.rust-lang.org/std/alloc/trait.GlobalAlloc.html)