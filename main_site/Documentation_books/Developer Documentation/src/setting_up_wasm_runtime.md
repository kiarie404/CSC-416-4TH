
####  Random
Random :
- wasm is an Instruction set because it specifies a set of instructions that can be understood by a virtual machine. The same way Riscv assembly instructions can be understood by a Riscv CPU. Riscv is an instruction set too.
- In computer science abtraction solves everything. When you want to standardize somthing... just virtualize accurately it and leave individual implementations to other people.
- Now to enable code to run on any machine... wasm group created a binary language/ an assembly language for a virtual machine. There are different implementations of this virtual machine. Those different implementations are called web assembly runtimes 

From Chat Gpt :
    Web Assembly (WASM) was designed to enable code to run on any machine, regardless of the underlying hardware or operating system. To achieve this, the WASM group created a binary format that can be executed by a virtual machine. This binary format serves as an assembly language for the virtual machine and is designed to be compact and efficient.

    There are different implementations of the Web Assembly virtual machine, each designed to run on a specific platform or operating system. These implementations are sometimes referred to as "Web Assembly runtimes" or "WASM runtimes." Examples of popular Web Assembly runtimes include the V8 engine used in Google Chrome and Node.js, the SpiderMonkey engine used in Firefox, and the Wasmtime runtime developed by the Bytecode Alliance.

These web assembly runtimes run on top of other systems. Eg on top of bare metal hardware, or in the browser, on top of operating systems.


### Questions
Which wasm runtimes can Run on bare metal?
    - wasmer
    - wasmi
    - wasmtime
    - wamr

Which one are we choosing?  
we will work with wasmtime or wasmer. They have very good documentation. They are up to date with WASM post-mvp features. They are maintained by core members of the Bytecode alliance. They are majorly written in Rust Programming Language. They have a mature Rust integration too.

Why not wasmi?  
- wasmi does not have a comprehensive documentation in comparison to wasmer or wasmtime.
- wasmi is implemented as an interpreter. An intepreter in the embedded space is inappropriate.

Why not wamr?
- It is written in C.

#### On wasmtime
- Wasmtime can be used both as a crate or a cmd-utility.
- we will use it as a crate. We will embed it in core Rust code.
- Someone should be able to take a .wasm file that is wasi compliant, load it in the OS as a user program and wait for output.

- You will not be able to install wasmtime itself as a user program in our OS... unless you install it as a .wasm fileS
- Our focus will not be on the commandline utility aspect.

##### Creating wasm files from highlevel languages(Rust) and running them.
- Install wasm32-wasi target. Install wasmtime-CLI. run .wasm files using wasmtime CLI commands
- But you can also go through the embedded way

##### The Embedded Way...our ticket to heaven
You can instantiate a wasm module inside Rust code using the crate ; [wasmtime](https://docs.rs/wasmtime/8.0.0/wasmtime/index.html).  
An instantiated module is a webassembly file that has been compiled and has been stored in memory... ready to be executed.  
Now let us breakdown that statement: 
    - Now the Engine has an internal compiler that takes in a raw file and outputs an optimized target-specific binary.(eg x86, Riscv)
    - We use this engine to first compile our .wat file into a .wasm file
    - You can use the default compiler configurations or you can pass a Config struct to adjust the compiler's behaviour. Soe of the things that you can adjust include : (a full list is [here](https://docs.rs/wasmtime/8.0.0/wasmtime/struct.Config.html))
      - Target architecture
      - Enable or disable outputting certain Performance Profiling metadata
      - Enable or disable outputting certain Debugging metadata that might be used by 3rd party debugging tools.
      - Disable code optimization or adjust the level of code optimization done by the compiler.
      - Enabling or disabling specific WebAssembly features. For example, the bulk_memory feature allows using bulk memory operations, while the simd feature allows using SIMD instructions 
    - Now 'storing in memory' means  that the webassembly runtime understood the memory requirements of the module and allocated the appropriate data structures and space within the memory that was asigned to the host application in the RAM.
    - Now from this definitions, an instance means 'a process' and the wasmtime runtime acts as the CPU.
    - So just like a normal process, the wasm instances are self contained and isolated. Self contained means that they have their own memory and all the imports were satisfied. Isolated means that the host application only communicates through imports and exports.
    - The host application code gets executed by the real CPU while the wasm instantiated module gets executed by the wasm runtime. *The WebAssembly runtime translates the WebAssembly code into machine code that can be executed by the CPU of the host machine.  so even though both codes get executed by the host CPU... they take different paths before they get there.

A Store is a collection of WebAssembly instances and host-defined state.  
The store is responsible for managing the state of instantiated modules. This state includes things like the module's memory, global variables, and table. The state of the module can be modified and accessed by calling its exported functions, but it is not directly accessible from outside the module or from other instantiated modules

We need to :
1. import the wasmtime crate
2. Compile the .wat module
   - Instantiate an Engine : "a compiler + a manager of instantiated modules under it"
   - configure the compiler found within the Engine
   - Compilation is done using something called an engine's compiler. It returns a compiled module called wasmtime::Module
3. Define a store
4. Define all the host functions that are required by the wasm  module
5. Function Wrap those host-defined functions




The WebAssembly page size, currently 64 kilobytes.

