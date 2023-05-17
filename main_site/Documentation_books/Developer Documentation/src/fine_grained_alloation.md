

- AllocationList - 
- The descriptor in this case is the AllocList Structure. It has 64 bits. It describes the state and size of a block. A block is a contiguous set of bytes.  
- The first bit of the AllocList structure states whether the block is taken or free. (1 == taken)
- The rest of the 63 bits represent the size 
- the smallest unit that can be allocated is 8 bytes because we set the alignment order at 3. When you request 3 bytes, you will get 8 bytes in return.
- The allocStrust


Look at the kmem.rs code in ch5 