# Rust Toolchain, Rust Targets

"mirin Brah?.... FUAAAARK!!!" - zyzzz


A toolchain is an english word. It means "tools" that are mostly used together, because their functionalities are in the same domain.   
For example, your gamepad, play-station and monitor create a toolchain for playing video games.     

A toolchain in Rust refers to the combination of tools used to develop rust programs.   
The specific tools include :
1. A compiler - "rustc"
2. A package manager - "cargo"
3. A build tool - "cargo"
4. A documentation generator 
5. Rustfmt
6. Clippy - analyzes your code and provides suggestions and warnings based on best practices, potential bugs, or suboptimal code patterns.
7. RLS (Rust Language Server) - 
8. Cargo Bench - helps you measure the performance of your code
9. Cargo Test - helps you in managing how unit tests and integratio tests get executed. 

## Why do we have different toolchains in Rust?

As earlier said, a toolchain is a bunch of software programs.   
There are many toolchains because the software programs within the toolchain usually have :
1. A specific version/ release date
2. It was compiled for a specific host (target)

For example : the compiler written for a x86-64-unknown-linux-abi will not run ontop of a x86_64-pc-windows-msvc
Another example : The Nightly version of the compiler might contain more funtionalities of the stable version.

## What is the differece between a toolchain and a target?

A toolchain contains a compiler that converts rust code into machine code.  
You need to tell the compiler which specific machine code you want to create. You do this by specifying the backend of the compiler to use. [undone - explain these page using diagrams and simple explanations]

