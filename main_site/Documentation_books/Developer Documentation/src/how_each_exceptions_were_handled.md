# how_each_exceptions_were_handled


[undone]

## 1. Instruction Address Misaligned [0]  
RiscV is a load/store architecture, meaning that if the CPU wants to load some value X from memory... and X is a 32bit word, then the CPU will ONLY load from a memory address that is aligned to 32 bits. If the CPU is instructed to fetch x from an address that is not aligned to 32 bits, it will throw an InstructionAddressMisaligned exception. It will also store the misaligned address into the mtval register.  

The same case applies for stores.  

The exception handler might resupply the CPU with an aligned address... or it may just terminate the process that is referencing misaligned addreses for its load and stores.   

In our case, we chose termination instead of recovery.  For Simplicity's sake.


## 2. Instruction Access Fault



InstructionAddressMisaligned, // 0
    InstructionAccessFault, // 1
    IllegalInstruction, // 2
    Breakpoint, // 3
    LoadAddressMisaligned, // 4
    LoadAccessFault, // 5
    StoreAddressMisaligned, // 6
    StoreAccessFault, // 7
    UserEnvironmentCall, // 8
    SupervisorEnvironmentCall, // 9
    MachineEnvironmentCall, // 11
    InstructionPageFault, // 12
    LoadPageFault, // 13
    StorePageFault, // 15