# Allocating  and Deallocating RAM Memory

Allocating means giving out free memory to processes or whatever.   
We need to decide on the smallest unit to allocate at a time.   

We have 3 choices ; we can allocate page-wise or byte-wise or both. 
For this project, I chose Page-wise allocation, Byte-wise allocations will be implemented in a future day... probably never..ha ha .    

A page in our system is 4096 bytes long. ie 4 KiB  

#### The allocation method
algorithm : alloc
inputs to alloc algorithm : the number of free pages required (required_pages)
Outputs to alloc algorithm : the address of the first page of a contiguous block of free pages (starter)
main goal : return an address to the first page of a free contiguous set of pages  : A RESULT VALUE (pointer/ error)

Steps:
1. Confirm that the number of required pages is more than zero.
   1. If number is zero or less
      1. throw an [Error M1](./errors.md)
      2. return the error to the calling function.
   2. If the number is more than zero... continue to step 2
2. Traverse the array of descriptors found in the heap
3. Try to Find a block of contiguous free pages
   1. If you find a block... skip to step 4
   2. If you traverse the whole array and you do not find space ... skip to step 5
4. Do the folowing :
   - update the descriptors that represent the block
   - return the pointer to the first page of the block
5. Do the following
   - return an [error_M2](./errors.md) indicating that there is no free contiguous space.


#### The de-allocation method
algorithm : dealloc
inputs to dealloc algorithm : the address of the first page of a contiguous block of pages that needs to be freed (starter)
Outputs to dealloc algorithm : The Result Type (Ok/Error)
main goal : deallocate 

Steps:
1. Check if the starter address is valid or not.
   - If the starter address is a null pointer... go to step 2
   - If the starter address is an out of range address... go to step 3
   - If the starter address is a valid address...go to step 4
2. Return a Result_Error showing that the process tried to deallocate a null pointer : Error_M3
3. Return a Result_Error showing that the process tried to deallocate a non-existent memory location : Error_M4
4. Loop through the allocated block page by page :
   - For every page...
     - clear the data by zero-ing the bytes within the page
     - Change the status of the corresponding descriptor to 'empty'
5. After the loop, return a successful message Result (ok) type

#### API 
- dealloc function
- alloc function


#### Testing this module
This module does 3 tasks. So we need to test all the 3 tasks.
1. Task 1 was : abstracting the heap into descriptors and pages.
2. Task 2 was : writing a function that returns the address of the first page associated with a free block of contiguous pages
3. Task 3 was : writing a function that frees a contiguous block of contiguous pages

##### Test 1 : Testing task 1 :
In this test, we compare preconfigured data that we calculated in theory and hope that our alocation function produces similar data.  
Confirm if the following values are similar:
- The heap_start address
- The heap_end address
- The number of data pages
- The number of descriptors
- number of descriotors == number of data pages

- Confirm that all the addresses of each page are a divisible of 4096
- Confirm that all descriptors are initially set to 'empty'

##### Test 2 : Testing Task 2, the allocating function
- confirm that submitting a zero to the function returns the appropriate Error 
- Confirm that submitting a value more than the number of pages found in a <128 MB heap will give the appropriate error.
- confirm that certain descriptor change after allocation : we should have a "first" and "end" and possibly a middle. But not 2 consecutive "firsts" or "ends".

##### Test 3 : Testing Task 3, the deallocation function
- confirm that submitting a null pointer yields the appropriate error
- confirm that submitting a pointer that is not within the heap range yields the appropriate error
- Confirm that all the data pages are indeed zeroed after deallocation and that they contain no garbage data or residue data.
- Confirm that all descritors involved in the deallocation process are updated to 'empty'


