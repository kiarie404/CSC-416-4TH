- Theseus is written in Rust. It is a language safe OS ie : A safe-language OS is an operating system designed to provide high levels of security and safety through the use of programming languages that have built-in safety features. These languages are designed to prevent common programming errors, such as buffer overflows, null pointer dereferences, and other memory-related errors that can cause security vulnerabilities and crashes.


- Theseus has a different architecture. It is not the traditional monolithic architecture nor a micro-kernel architecture. Everything runs in the same address space, under priviledge mode. 
-  everything runs in a single address space (SAS) and single privilege level (SPL)
-  It takes a cell structure. (cytokernel) This is an attempt to make the system as modular as possible.
-  A cell is similar to a Rust Crate. A rust crate a Rust crate is a self-contained unit of code that can be compiled into a library or executable, and can be shared and reused by other Rust projects. A rust crate usually comes with a dependency manifest tree writte in its cargo.config file. A rust crate is the most basic unit of compilation. 
When you run the Rust compiler, it compiles each crate independently, and then links them together to produce the final executable or library. This approach makes it easy to manage dependencies between crates, and enables efficient incremental compilation of Rust code.

Within a crate, Rust code is organized into modules, which can be used to group related functionality together and manage the visibility of code. Modules can be nested, and can include other modules, structs, enums, functions, and other items.

Overall, the combination of crates and modules provides a powerful mechanism for organizing and sharing Rust code, and makes it easy to build large, complex applications and libraries with Rust.

Each crate is private by default and the programmer has to write a public API for each crate, accompanied with autoamtic compiler documentation.

But in Rust dependencies are tracked at crate level. On the other hand in Theseus dependencies are tracked in "section level" to make it more fine rgained


Theseus OS disregards hardware isolation protection. Hardware protection is fast and efficient under certain circumstances...but it is not a sure bet. Look at the Meltdown and Spectre. Theseus Largely depends on software defined protection ... at least we get what we write:  it relies on type safety and memory safety guarantees from the Rust language and compiler to enforce protection and isolation between tasks and components. 

everything must be written in a safe language like Rust or Haskell, else things might fall apart... terribly

But why does THeseus not depend on hardware Isolation?
    - 

#### Whats different and same
- There is no user space or address space
- Independent of hardware isolation, disregards hardware isolation
- Web assembly based

#### Whats same
- Rust



#### My take 
- Hardware Isolation is not a surebet, software enforced isolation is also not a sure-bet.
- Now that we have two half-bets, using both of them when necessary is a better strategy. this might introduce some complexity but it is a worthy trade-off.
- Making it mandatory to use Web assembly modules as the basis for the cells is a better option. This gives people the option to use both safe and unsafe languages without the fear of breaking the OS just because they did not use Rust or Haskell to write their application. We are in a world where some unsafe languages have become standards in certain domains eg : python --> Data science and AI, Javascript (web-based applications), C,C++ console applications. In as much as there are cool languages, that will not stop people from sticking to unsafe languages out of preference or other valid reasons. The downside of webassembly would be the wasm runtime  performance issue... the wasm programs are slower than native programs.
- I like the idea of modularizing the OS into self-contained cells I like the idea of having two steps of enforcing complete dependancy checks between the cells (namespace level and section level) 
- I like the Idea that we use the Rust compiler checks to test the integrity of the Kernel at build time.
- I like the idea of unsing a safe languageto construct an OS