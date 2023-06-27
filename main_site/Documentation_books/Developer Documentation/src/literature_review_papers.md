# Literature review papers

Here is a list of references that will guide the following sub_chapters :
1. Bringing WebAssembly Up to Speed with Dynamic Linking - [source](https://helda.helsinki.fi/bitstream/handle/10138/335259/WAsDE_SAC2021.pdf?sequence=1#cite.taivalsaari2018development)
2. Architecting the Web of Things for the fog computing era - [source](https://ietresearch.onlinelibrary.wiley.com/doi/full/10.1049/iet-sen.2017.0350)
3. On the Development of IoT Systems - [Source](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwiw6vuuxZj_AhX_m_0HHW_9CD8QFnoECAoQAQ&url=https%3A%2F%2Fresearchportal.helsinki.fi%2Ffiles%2F151313168%2Ffour_corners_iot_1.pdf&usg=AOvVaw0R8kUvpQkK2rporht2l2F0)
4. Isomorphic IoT Architectures with Web Technologies - [source](https://helda.helsinki.fi/bitstream/handle/10138/336610/Isomorphic.pdf?sequence=1)



#### From [1]
We need to accomodate writing software for heterogenous computing. (ubiquitous computing)   
IoT devices require both local support systems and cloud systems. (fog computing and edge computing)
Edge computing insists that computational tasks get done by the end bots only, while fog computing allows computation tasks to be done by bots, near_nodes and cloud.     
But in todays world, fog computing and edge computing can be used together. No_one cares. It is very hard to build anything as pure edge. There is no need to put names on things






    


#### From [2]

Fog computing involves creating an architecture where data processing is distributed across devices located closer to the point of data collection, instead of relying solely on a centralized cloud infrastructure.

It is not enough to depend on a central server for all the tasks.   
You need to choose wisely which tasks get doe centrally and which tasks get done "almost locally in a distributed manner".  

Now we are going in an error of programmable matter. We cannot depend on the traditional cloud-server architecture because it is slow. The network transfer time is very expensive. Programmable Matter co-ordinations need to be real time fast.  

Example : Your security system at home uses the camera, thermostat and automated machine gun. These devices need to co-ordinate fast. It is unimaginable that for them to communicate they have to first communicate to a far remote server. That is very slow. More time is spent on message transfer than actual data processing.

The solution is fog computing, where you have a local server nearby. Or one of the devices(eg camera) takes up the role of a server.



### Considering WebAssembly Containers on IoT Devices as Edge Solution
[source](https://liu.diva-portal.org/smash/get/diva2:1575228/FULLTEXT01.pdf)

- we need to cover the :
  - wasm containers
  - wasm container runtimes
  - wasm ecosystem (management of both the runtimes and containers)

- Under wasm runtimes, we will cover their :
  - speed of execution
  - memory footprint
  - functionalities provided and how they can be extended
  - security (internal security + external security)
  - future

- used wasmer and wasmtime as the test subjects (both written in Rust) BUT wasmtime uses the CraneLift compiler while wasmer offers the option to use Singlepass(AOT), CraneLift(rust JIT), and LLVM(AOT/JIT). Wasmtime is made by the Bytecode Alliance while wasmer is made by the Wasmer private organization(I don't know their name)
- WebAssembly Runtime is essentially an environment where Wasm can be executed safely, it provides an abstraction layer above the OS, containerizing the execution.

- Why not Docker?
  - slower startup time. Meaning it might be wasteful for an event_driven IoT device
  - memory heavy in comparison to (wasm container + runtime)
- Why Docker?
  - known, docs, tutorials

- Why not native?
  - not capability_based security by default
  - Not as secure as with wasmRT (why?)
  - portability when dealing with heterogenous IoT devices. We need to migrate code + libraries more easily.

- Why native?
  - fast execution because there is no boundary between code and OS. We are taking into account AOT runtimes. JIT and interpreters are out of the picture.
  - optimization of native machine code is not an excuse because the AOT compiler insude the WasmRT also does optimization of the native machine code

What are our benchmarks when determining when to use? C vs wasm_binary vs Docker_solution
- Runtime memory usage
- Runtime execution time 
- container image/executable image memory usage
- container image/executable execution time
- container image/executable startup time (important becuse of event driven IoT apps?)

Limitations of paper :
- used raspberry pi model 3 only (it is generic iot platform)
- tested wasmer and wasmtime only
- did not benchmark qualitative aspects of development of programs with Wasm eg ease of development and maintainability or debugging (a major challege in IoT development)

The paper Uses PolyBench/C - is a suite of 30 different numerical computations, ranging all the way from linear algebra to image processing and physics simulations, written in C

PolyBench/C has both shorter, burst like jobs, as well as longer; it also has workloads that uses less memory, and workloads that uses substantially more. This shows both the strengths and weaknesses of Docker and Wasm, as they can show how well they perform in the different subsets of these workloads.

The bias of using PolyBench/C is that it tests many kinds of algorithms. It is better to test for your specific kind of algorithms instead of general algorithms.    

Test : 
    - run each PolyBench Algorithm ten times. Find the average. call it x
    - plot a graph that shows each test runtime or memory usage.


So when you use wasm runtimes in userspace you get 2 levels of security :
- Security provided by runtime
- security provided by OS kernel eg (file systems, MMU, memory randomization, process isolation)

One layer of security is enough

Why wasm ?
- better security than native
- code portability for heterogenous devices (hardware, capabilities, software and configs) in the rig
- smaller memory footprint than docker containers
- faster startup time that Docker when using a wasmRT that uses JIT/ interpretation technology
- faster execution time than docker  
- Developers can write in languages that they are profecient in
- Wasm container size is smaller than native executables by averagely 14.7% (Andreas Rossberg, Ben L. Titzer, Andreas Haas, Derek L. Schuff, Dan Gohman, Luke Wagner, Alon Zakai, J. F. Bastien, and Michael Holman. 2018. Bringing the Web up to Speed with WebAssembly.)
  - 

So wasm brings portability and security at the cost of performance. 

Why is native code execution faster than wasm code execution(AoT execution in this case)?

* JIT compilation
* AOT compilation of wasm binaries to enhance speed
* Interpretation

What is the work of a runtime? Security? dependency sorting? What about AOT compiled wasm modules

Does that mean that the kernel should provide the option of AOT compilation for certain tasks and JIT compilation.  
But why do we need JIT compilation in Embedded systems? In embedded systems performance and efficiency is our first priority. So we are not considering intepreters or JIT compilers

So we need the runtime so as to execute the native code and further enforce sandboxing features. Not for JIT compilation or Intepretation.
The runtime interacts with the underlying OS during the execution process. It acts as a manager for the compiled wasm modules.  

A runtime does the following functions :
- Load the wasm modules from storage
- Validate the format of the wasm module
- Compile the wasm module to target machine code. (using AOT or JiT or pure interpretation)
- Interact with the OS system interface in order to initiate the execution of the compiled code . (eg loading code into ram, making syscalls, allocate heap memory)
- Implement WASM sandboxing features

So when you use wasm runtimes in userspace you get 2 levels of security :
- Security provided by runtime
- security provided by OS kernel eg (file systems, MMU, memory randomization, process isolation)


so the runtime is a sandbox environment : 
We use it because it offers the following security implementations :    
    Memory Isolation: Wasm runtimes employ memory isolation techniques to prevent unauthorized access or modification of memory. They use memory sandboxing mechanisms such as linear memory boundaries, memory segmentation, or address space layout randomization (ASLR) to isolate Wasm module's memory from the rest of the system.

    Access Control: Runtimes enforce access control policies to restrict the Wasm module's ability to interact with the host environment and sensitive resources. They provide mechanisms to control and limit access to system APIs, file systems, network interfaces, and other potentially dangerous operations.

    Sandboxing: Wasm runtimes leverage sandboxing techniques to isolate the execution of Wasm modules from the underlying system. They create a restricted environment where modules operate within predefined boundaries, preventing them from performing malicious or unauthorized actions. Sandboxing techniques may include process-level isolation, operating system-level sandboxes, or virtualization technologies.

    Code Verification: Wasm runtimes validate and verify the integrity and safety of Wasm modules before execution. They perform checks on the Wasm module's binary format, ensuring it conforms to the specification and does not contain malicious or malformed code. Verification helps prevent code injection, stack overflows, or other common vulnerabilities.

    Controlled Resource Access: Runtimes enforce restrictions on the Wasm module's access to system resources, such as limiting file I/O operations, network requests, or access to hardware devices. They provide APIs or interfaces that allow controlled and supervised interaction between the module and the host environment.

    Runtime Sandboxing: Some runtimes implement additional sandboxing techniques at the runtime level. They establish secure execution environments with limited privileges, where Wasm modules can execute without compromising the security of the hosting system. This includes isolating the module's execution from the runtime itself and other concurrently executing modules.

    Capability-Based Security: Wasm runtimes may adopt capability-based security models, where explicit permissions and capabilities are granted to Wasm modules. Modules can only access resources or perform privileged operations if they possess the necessary capabilities, effectively limiting their privileges and reducing the risk of unauthorized actions.

    Security Auditing: Wasm runtimes undergo rigorous security audits and testing to identify vulnerabilities and mitigate potential risks. Regular security updates and patches are released to address discovered vulnerabilities and ensure the runtime remains secure and resistant to potential attacks.



#### WebAssembly Modules as Lightweight Containers for Liquid IoT Applications

Even if we implement the wasmOS, we need to meet the following requirements in order to fully replace docker :

- application state synchronization (make it more mature)
- dynamic orchestration of the apps : spawning of containers, deleting containers across different nodes. (kurbenetes supports wasm containers : This project, called Krustlet [22], allows Wasm containers to be run as Kubelets in Kubernetes.)
- Fine grained security model 


#### From Wasmachine

- * I do not know the security features of wasm runtime compilers : how do they ensure integrity in the control flow? For wasm machine, the AOT compiler ensures cotrol flow integrity by :
  1. makes code segment immutable to avoid direct code injection 
  2. the compiler adds code that verifies the signature of indirect functions. eg virtual functions
- WASI is an API. It has different modules inside.
- An API defines functions and data structures for interaction between 2 systems.
- Wasi has many modules. For example we have the Core Module that describes an API that is common to many Operating systems eg File_functions, networking functions. We have other non_core modules such as Crypto.

- A webassembly module without function imports can only do math... keeping the CPU warm.
- A wasm module can import a host function. This host function can do other things like file manipulation or networking.  
- Now you still have to specify the specific host functions your module is using (capability based search).
- 
- WebAssembly modules interact with the outside world exclusively through APIs. 
- WASI file system rules _ sandboxing feature [here](https://github.com/bytecodealliance/wasmtime/blob/main/docs/WASI-capabilities.md)

- Function normalization happens during compilation. So if we use an AOT compiler, the normalization latency will not affect us at runtime. If we use a JIT or an interpreter, the normalization latency will affect us at runtime.

- Here is an example of normalization : Normalization happens because there is a difference in function prototypes between the WASI API syscalls and the Native Syscalls.

- We can eliminate the latency caused by normalization by .... (DRUM rolls...) eliminating the need for normalization. We will do this by making the underlying system to export its system calls using the WASI function prototypes.   

- Now the remaining latencies are the latencies experienced during : 
  - interception of syscalls by the runtime. Interception means inspecting the syscalls and making sure they follow runtime security rules...
  - Context switching from user process to kernel process.  

The only way to remove the inspection latency is to remove the inspection stage. We cannot afford to remove the inspection stage. The inspection stage enforces capability based security.

To get rid of the context switching expense, we eliminate the need for the OS to do context switching from user_process to kernel_process. THe only way to completely avoid a context switch is to make the wasm_process part of the kernel code. Such that the wasm_process is just a function within the kernel. But this would mean that we have to compile the app together with the kernel. Such that if we want to update the wasm app, we hae to recompile the entire kernel and then re-install it.   But if performance was more important than maintainability, then this is the way to go.  

If your wasm App needs no future updates regularly you can use the above method. WASMACHINE uses this method. The only difference is that they execute these "wasm app functions" as Threads (this is both good and bad) Critic this (hard to implement threading in bare, no updates for both apps and OS, hard for installed apps to communicate easily)
(simple to implement, fast, optimized during compile time)


For our method, we wil not get rid of context switching completely. This is because we want to implement the wasm app and the kernel as seperate processes. So we will just reduce the expense of context switching. 

Context switching from a user_level process to a kernel_level process is more expensive that context switching from a kernel_level process to another kernel_level process. This is because switching from user_level process to a kernel_level process requires more overhead : You have to switch modes, re-map the page tables and switch the execution stack. On the other hand, you don't have to switch modes or re-map the page tables


sandboxing nature so far :    
- only interact with outside resources via host function imports only... other than that, you are just keeping the CPU warm.
- specify the specific host functions that a module uses. This will help in scanning which module is using host functions that are not meant to be used by it.
- The runtime configurations ensure that the modules affect resources that it has been granted. For example, the config --dir=. says that 
1. the wasm modules being executed by the runtime can exercise the file host functions that they have imported. ie '--dir'
2. The wasm modules can only affect the current file directory ONLY. Not the entire file system like in UNIX.
- wasm modules use a linear memory that is accessed through base + offset. The wasm module can only access this linear memory. Any out-of-bound access will be detected at compile time.


The case for wasmachine :
- completely eliminates context switches therefore fast : . The results show that WebAssembly applications in Wasmachine is up to 11% faster than their native opponents in Linux. 
- It uses a runtime that has AOT capabilities, meaning that the wasm apps are compiled to native... the only performance bottleneck becomes the systemcall inspections. can you imagine that... inspections... a bunch of majorly read operations
- The final extended runtime does not include the compiler (be it JIT or AOT), it just contains the runtime manager. THis is because the compiler is a different 
- The wasmachine 


Why no JIT or intepreter
- Too Compute intensive for embedded environment (constant loops of compilation) - bad for battery and processing power sharing
- Time inefficient (no optimaization like in AOT)
- unpredictable performance in JIT due to dynamic optimization

