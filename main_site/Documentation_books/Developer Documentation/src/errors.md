

### Under the Memory Management
1. #### Error_M1 : 
The allocation function was requested to allocate no Pages. This is like going to the shop and telling a shopkeeper to sell you nothing. There might be a problem with the calling function.


2. #### Error_M2
   - Reason for Error :The RAM has no contiguous free pages that are equal to the number of pages requested. 
   - Possible causes  : 
     - The heap in the RAM has space... it is just that the available space is not contiguous. The available space is scatterd in fragmnets.
     - The RAM is fully occupied and there is no extra space.
   - Possible solutions :
     - Defragment the heap
     - Add more RAM to the machine
     - Close other processes so that some space in the RAM can be freed
     - 
#### Error_M3
   - Reason for error : The program tried to de-allocate a null pointer. You cannot de-allocate nothing. 
   - Possible causes  : The program written by the programmer tried to free an null pointer. Fix your code brah
   - Possible solutions : Fix your code... ha ha


#### Error_M4 
   - Error              : The program tried to access and deallocate an address that is not found within the heap section
   - Possible causes    : The address is not within the address ranges specified by the linker script
   - Posible solutions  : Find an address that is within the range : _heap_start and _heap_end. Moreover, the address should be within the segment of the heap where data_pages are stored.
1. Error_M5