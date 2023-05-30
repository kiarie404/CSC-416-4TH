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

- used wasmer and wasmtime as the test subjects (both written in Rust)

- Why not Docker?
  - slower startup time. Meaning it might be wasteful for an event_driven IoT device
  - memory heavy in comparison to (wasm container + runtime)
- Why Docker?
  - known, docs, tutorials




* JIT compilation
* AOT compilation of wasm binaries to enhance speed
* Interpretation

What is the work of a runtime? Security? dependency sorting? What about AOT compiled wasm modules

Does that mean that the kernel should provide the option of AOT compilation for certain tasks and JIT compilation.  
But why do we need JIT compilation in Embedded systems? In embedded systems performance and efficiency is our first priority. So we are not considering intepreters or JIT compilers

So we need the runtime so as to further enforce sandboxing features. Not for JIT compilation or Intepretation.
The runtime interacts with the underlying OS during the execution process. It acts as a manager for the compiled wasm modules.  

A runtime does the following functions :
- Load the wasm modules from storage
- Validate the format of the wasm module
- Compile the wasm module to target machine code. (using AOT or JiT or pure interpretation)
- 