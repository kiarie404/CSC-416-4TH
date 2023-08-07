# Unit Tests

Theory Reference : [Rust By Example Chapter on Unit tests](https://doc.rust-lang.org/rust-by-example/testing/unit_testing.html) 

Unit tests are tests that target features of a specific module. For example, if you vave built a robot that is divided into head_module, arm_module and chest_module... tests that fous on the arm_module ONLY are called unit tests.   

In this project, the following modules have unit tests :
1. sv39_mmu module



To run the Unit tests, write the comand "cargo test" in your terminal.  
To disable the unit tests of a certain module, navigate to the tests file of that module and comment out the switch function.  
[undone] : make this testing to thing be easy to understand, the hell?  
[undone] : write the unit tests for each module


## Tradeoffs taken :
- Not every independent module has unit tests
- For the tested modules, not every function has been tested.  
- For the sake of covering what's important, almost all public functions of each module has been tested. It's the private functions that have been largely neglected.  
- The RIscv Assembly files DONT have test functions
