
These are notes made from the book : Programming Web Assembly with Rust by Kevin Hoffman.



## Intro
- Tech comes and goes; client-server, thinclient, fatclient, full_blown_cloud native.   
- The web can be seen as a global computer.
- How will Wasm change the way developers build applications/ software? How is it revolutionary?
  - I thought it just changes the way you compile applications?
  - I thought it was just another bytecode? Or another universal elf file?

- Kevin says that wasm will change :
  - how consumers interact with apps
  - how developers build applications
  - the kind of applications a developer can build (WHAAAAAAT???? I thought every language was turing complete?)
  - It could change the definition of the word appliation

### Todays Web Technology
- Web tech has improved : faster transfer speeds of data for any app, better browser engines for clients to run web apps, Better local machines to run those browser engines.       
- In other words, The global computer (the web) has become faster, more efficient and has much better processing units(RAM and CPU and HDD)


- Wasm is still maturing, it lacks tools. But which tools?  
- It has pain points, But which are these pain points?
- We need tools for : 
    - How to interface host systems with wasm modules eg Browser virtual machines, OS virtual machines
    - How to orchestrate wasm containers
    - How to do security scans and management on wasm containers
    - How to reconvert wasm code to readable high level code (so that people who speak different programming languages can understand each other.)
    - Handling wasm modules in a language independent manner


## Fundamentals
WebAssembly can be viewed at two different levels—the raw, foundational level and at the higher level of other programming .languages using WebAssembly as a target.
To understand fundamentals means that we need to Understand what web assembly can do, what it can't, and how to use language independent tools to deal with wasm modules

This way, you can understand how higher level languages produce wasm modules as a compilation product.

[promise]
**One problem solved using wasm is that people can stick to the languages, tools and development styles they like. Regardless of whether they are in backend, frontend, embedded space** - There are paradigm shifts between this spaces. And grass isn't always greener on the other side.  What if you did not have to change your language when switching between this domains?

You are no longer tied to JS or python if you want to go ubiquitous. You can use any library written in any language, al you have to do is to compile that library to wasm. 

Wasm brings the message : You can code in the language you like, for any domain. No need to learn new languages. You can choose your own language. Be assured will run on anything that can habitate a wasm runtime. Be it a browser, desktop or a satellite. You concentrate on writing the business logic

You are no longer tied to Java in order to run things everywhere. Or The C# frameworks for making it okay to write for frontends and backends. Or Javascript to develop web applications. Write software in a language you want, it will run

Let us end this disease of tooling so that developers can concentrate on implementing business processes.   

Wasm is different from Java because it :
    - allows the use of other high level languages by default
    - uses a capability based security architecture.


### Wasm
- **Portable binary code** - has a bytecode that can be translated to existing assembly languages for different CPUs architectures. A generic bytecode. 
- **for a stack based machine** - the bytecode instructions are designed for a stack execution. This has its advantages and disadvantages that affect its code size, code sped and code     

    advantages :    
    - smaller binary size
    - ease of portability : unlike in registers where some registers have predefined responsibilities mentioned in the ISA Convention. The stack does not have specifics about its stacks.
    - I do not understand the rest yet.

    Disadvantages :
    - limited parallelism support. The stack is designed to be unwound sequentially. It is cumbersome to implement parallelism. It requires additional stack management activities.
    - Unintuitive for most programmers making it hard to debug



Why is Wasm not a JS killer? 
    - Because most browsers have a JS virtual engine. This is a big project. Turning it to be fully wasm-only will be hard.
    - JS has many default features and tools purposefully made to suit web development eg event handling. A language like rust is turing complete yes... but you have to start building new tools around it. Like Yew.
    - Many developers building for the web use JS or typescript. Why change? 

For Me, wasm matters because I am not delving into web development.

A wasm module on its own cannot do anything. It needs to run within a runtime system that can interact with the host. THe hos in this case can be the OS or an ethereum API.


#### Datatypes and control flow
- it only has 4 data types : i32, i64, f32, f64.
- Whether they are signed or unsigned can be specified during operations eg i32.add  or i32.add_u

#### Linear memory
Linear memory : a static set of cotiguous memory. You can grow this linear memory in pages (64KB each). You do this statically. The runtime will not do this for you.   

The linear memory that a module uses can be :
    - decalred by the code inside the module
    - exported by the code inside the module
    - imported from the host using the code inside the module.

**Why linear memory?**  
- direct memory accesses are mad fast. Causing efficiency. You index the linear memory like an array. Using offsets as the index.
- The wasm module can only read and write from this linear memory. The module cannot read and write to the hosts memory or any other memory that is not its linear memory [security]

**But the host can access the wasm linear memory? Isn't this a risk?***[security]
Yes this is a risk. But this risk has been nullified by requiring the host to cause change to the linear memory by using the functions exported from the module. In short the module says to the host : "I will let you control my spaceship, but you will only show you the buttons that I want you to press... and I will define what those buttons actually do in the background" 

**in the end, the runtime is the first place to attack***   
this is because the runtime is the one that enforces these interaction rules between the host and the wasm module.  
The choice of runtime becomes important


## Language Independent Tooling
- we will use the WABT - web Assembly Binary Tools
- This wabt is a set of tools ; but we will only need wat2wasm and wasm-objdump
- wat2wasm converts a .wat file to a .wasm file.
- wasm-objdump helps you analyze :
  - see which memories were exported, or imported, or created -- and by which names
  - see which function signatures exist
  - see which functions exist (by name) and which signatures they use
  - see which functions exist (by name), which ones were imported (and their import name)
  - which are the global variables
  - what are the dependencies of the module?
  - other things that I currenntly don't need to know yet
  - You can disassemble the machine code and see the assembly code with memory locations of the instructions outlined
  - You can see the memory mapping of each header

commands : 
```bash
sudo apt install wabt             // installing WABT
wat2wasm test.wat -o test.wasm    // using wat2wasm
wasm-objdump test.wasm
```

You can embed wabt in rust code.    
You can embed wasmi and other runtimes in wasm code.

# Outside the Browser

Tasks you need to know:
    - load a wasm module
    - interpret/compile
    - execute the interpreted/compiled code
    - How to avail host functions
    - How to use wasm exported functions
    - How to use memory exported from a wasm module
    - How to import memory from the host

### How to be a good Runtime. The functions of a Runtime

The WASI interface specifies the contract of interaction between host and wasm module:
    - The syscalls
    - The data formats, communication protocols that both Host and Module must follow for interoperability.

What does it mean ... "run in the runtime sandbox"?     
    The runtime is just a program. It also gets packaged as an elf file filled with machine code.   
    The wasm file gets turned to machine code too.  
    Running as a runtime means that the wasm_machine_code gets embedded into the elf file of the runtime program.   
    The runtime adds management instructions to the wasm_machine_code.  
    For example ; wasm_code -> check_syscall_permissions -> validate_syscall_execution -> wasm_code(that makes syscall) -> 

    The runtime also adds glue code :
        - links the WASI calls to the native Syscalls
        - links host functions to the orrect exported wasm functions

* draw a diagram here kid


The functions of the host include :
    - loading the wasm module : reading the bytes and storing them in a buffer

    - validating the wasm module : check for wasm syntatic, semantic and security specifications eg:
      - check type safety : eg in function signatures and return values. Or operations that act on wrong types
      - validate cotrol flow integrity
      - ensure that the memory references are within linear memory
      - ensure other run_time specific specs eg : 
        - checking for access rights 
        - check if imported functions are available

    - Expose the wasm module exports to the Host
        A wasm module without an export just keeps the CPU warm.    
        A wasm mobule provides a way for the host to use its functionality by exporting functions.  
        The Runtime reads the exports and provides a unified working and API to the Host
        The runtime compiled elf file will contain code that links host functions to wasm exported functions

    - Satisfy imports needed by the Wasm Module from the Host
        The wasm module may import 
            - host functions 
            - linear memory.
            - Global variables (for communication or config purposes)
            - Tables (This is an array of function references from the host or other wasm modules)
            - Host custom data structures (eg a driver struct)
            - A high level API (almost same as a custom data structure or a Table)
        
        The runtime needs to make sure that the imports are satisfied. And if they are not satisfied, it should take the appropriate response... depending on the issue.    
        eg. If the linear memory is not available, then it should return the error : "Host does not hae enough free memory space"   

    - compilation or interpretation
    - Execute 
    - Module Isolation from other modules (manage exported and unexported items accordingly) - This can be achieved by executing the runtime elf as seperate threads for each independent module


## Building a Runtime in Rust (but not from scratch)

### Wasmi specifics
In order to execute code from a wasm module, it must be instantiated. Instantiation includes the following steps:


steps :
    1. Loading
    2. Validating 
    3. Creating a module instance with no imports satisfied and no instant bootstrapping
    4. Resolving the definition instances for each declared import in the module
    5. Instantiating definitions declared in the module (e.g. allocate global variables, allocate linear memory, etc.).
    6. Initializing memory and table contents by copying segments into them.
    7. Executing the start function, if any
    8. After these steps, the module instance is ready to execute functions

#### 1. Loading
- create an empty character buffer, preferrably a vector because of unknown size.
- copy the contents of the .wasm file to the buffer.
```rust
let mut buffer = Vec::new();
{
let mut f = File::open("../fundamentals/add.wasm")?;
f.read_to_end(&mut buffer)?;
}
```

#### 2. Validating wasm code
- convert the wasm code into a format that wasmi can validate, and validate it.
- All this can be done using the "let module = wasmi::Module::from_buffer(buffer)" command
```rust
let module = wasmi::Module::from_buffer(buffer)?;
```

#### 3. Creating a module instance with no imports satisfied and no instant bootstrapping
The runtime needs to resolve imports demanded by the validated wasm module.     
The following elements can be imported : linear memory, global variables, functions, tables, 
So we use some structs called ImportResolver to resolve each of the elements.   
An ImportBuilder calls a bunch of ImportResolvers.  
An ImportBuilder::default() does not call any resolvers.   

The import builder outlines the host's element signatures and unique index . 

A ModuleInstance .assert_no_start(); function panics if the wasm code contains a start function. It prevents automatic bootstraping of modules... for security purposes.    

If we wanted a module with a Start, we could have called ModuleInstance .run_start()

```rust
let add_import_builder = ImportBuilder::default();
let instance = ModuleInstance::new(&module, &add_import_builder).expect("failed to instatiate module").assert_no_start();
```

#### Step 4,5,6 do not apply in this example.
This is because the module does not have imports(4) and it has nothing to be instantiated or allocated.     

#### 7. Execute wasm_exported Functions
When you pass values to a wasm function, you must turn them to RuntimeValues. A RuntimeValue can be I32,i64,f32 or f64.     
Values that are returned from wasm functions are also in Option<RuntimeValue> form. You need to unwrap them as enums.   
You can call a wasm function using the 
```rust
let mut add_args = [RuntimeValue::from(1), RuntimeValue::from(2)];
let wasm_return_value = instance.invoke_export("add", &add_args, &mut NopExternals);
```

The name of wasm_exported function is case-sensitive.   
When you invoke a function from wasm_module, you can give the wasm module the external resources to use using a type that implements an External trait.   
The 'Externals' trait allows you to define which host elements used by the wasm module.

In our case, executing the add function needs no external resources such as host_functions and global variables. 



#### Pattern 
 - resolve imports
 - define external resources
 - wrap the an API around wasm_module_exported function invocations.
