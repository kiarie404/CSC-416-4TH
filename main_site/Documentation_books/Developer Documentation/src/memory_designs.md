# Designs

## Birds View - Which memories are being dealt?

```mermaid
stateDiagram
    [*] --> RAM
    [*] --> Dedicated_I/O_region
    [*] --> Hard_Disk

```

## Abstraction of the RAM
![](images/memory/RAM_abstraction.svg)  

## Heap Abstraction
The Heap has been divided into 2 sections.  
A part dedicated for the Kernel heap allocations and a part dedicated for loading user programs  
Each user program can grow their heap.  

![](images/memory/Heap_division.png)

## Paging
The Page Manager uses descriptors to keep track of allocated and free pages.    
The Page manager can help you allocate or deallocate a cotiguous group of pages. Each Page is 4096 bytes large.  
Memory initialization is the process of demarcating the heap into balnced Descriptors and Pages.  

### Descriptor Ordering
![](images/paging/Descriptor_ordering.png) 

### Wrong orders
![](images/paging/wrong_order_descriptors.png)

### Paging Process
```mermaid
graph LR
    page_request --> allocation
    subgraph allocation
        start -->|Request x amount of pages| Desciptor_scanner
        Desciptor_scanner -->|looks for x contiguous empty descriptors| C1{did it find a block?}

        C1 -->|Yes| Populator
        C1 -->|No| Return_exception

        Populator -->|updates the descriptors| Success
        Success -->|Returns the address of the first Page| End
    end
```


## Byte allocation
Byte allocation happens within the Page. A linked List is used to keep track of which bytes have been allocated

![](images/byte_allocation/abstraction.png)


## Virtual Memory Protection

![The MMU](images/MMU/the_MMU.png)

### The Page Table organisation

