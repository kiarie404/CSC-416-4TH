# No-std testing    

references :    
1. Normal Testing : https://doc.rust-lang.org/book/ch11-00-testing.html
2. No_std Testing : https://os.phil-opp.com/testing/
3. Probably This : https://ferrous-systems.com/blog/test-embedded-app/


Rust has a built in test framework. Nice.   
But this framework uses a "test" library which is dependent on the std library.  

But Rust gives you an option to use a custom test framework.    
You have to activate the unstable feature called : [custom-test-frameworks](https://doc.rust-lang.org/unstable-book/language-features/custom-test-frameworks.html)  

The feature requires you to :
2. Define a runner function that takes in all of the test functions collected in the form as an array and executes them to your specification.    As seen, you can specify how the tests are run, which tests get to run, and how the test results get displayed. You are free to code the behavior of your test handler.   
3. sds


## Steps 
1. Activate the feature across the crate by adding this to the root of the crate (in our case, main.rs):   
```rust
// in main.rs
#![feature(custom_test_frameworks)]
``` 
2. Specify the path of the test runner function, describe this path crate-wise. To be specific, state the test runner in the root of the crate. The test runner function can be code found in another module. You can go wild   
```rust
// in main.rs
#![feature(custom_test_frameworks)]
#![test_runner(crate::custom_function)]
``` 
3. Set up display system. You can use your own machine drivers or an external console. Just find anway for print and println to work.
   - Add println
   - Define a good panic handler function with println capabilities
4. Define the runner function. as follows :
```rust
#[cfg(test)] // make cargo test consider this module
fn custom_function( bunch_of_tests: &[&dyn Fn()]){ // funtion accepts an array of functions that have been tagged with the #[test_case]
    println!("HAndling {} tests", bunch_of_tests.len());
}
```
5. now the test framework exports its entry point function as main(). But in this environmet, main is ignored. You need to intergrate the entry point of the test framework as part your current entry point.  
6. So we make the test framework define its entry point with a name that we specify. Like this : 
    ```rust
    #![reexport_test_harness_main = "custom_name_of_entry"]
    ```
    and then you call this function under your current program entry point
7. Here is the full layout : 
```rust
#![no_std]
#![no_main]
#![feature(panic_info_message)]
#![feature(custom_test_frameworks)]
#![test_runner(crate::handle_tests)]
#![reexport_test_harness_main = "custom_name_of_entry"]

mod asm;
mod stdout;
mod drivers;
use core::panic::PanicInfo;

#[panic_handler]
fn panic_handler(panic_info: &PanicInfo) -> !{
    let message = panic_info.message().expect("No panic message found");
    let file = panic_info.location().unwrap().file();
    let line = panic_info.location().unwrap().line();
    let column = panic_info.location().unwrap().column();
    println!("Panic : ");
    println!("File and Line: {}, {}", file, line);
    println!("message : {}", message);
    loop {}
}

#[no_mangle]
pub fn kmain(){
    println!("Hello world");

    // #[cfg(test)]   
    // main();     // will not work, you need to define it with a new name

    #[cfg(test)]
    custom_name_of_entry();
}

#[cfg(test)] // make cargo test consider this module
fn handle_tests( bunch_of_tests: &[&dyn Fn()]){ // funtion accepts an array of functions that have been tagged with the #[test_case]
    println!("HAndling {} tests", bunch_of_tests.len());
}

#[cfg(trash)]
fn trash_function(){
    println!("I am trash");
}

```
## How to print test results
- define custom assert
- use procedural macros to print function name/ For now just write custom messages as demonsstrated in the RustConcepts place   

## How to run tests module-wise

## How to filter tests
The thing is, this setup runs every #[test_case] function in the Crate. Now we want to make it do things in a filtered way. Perhaps module wise. 

To do this you have to create a function that creates an instance of &[&dyn Fn()]s that you want, and then pass it to a handler (ie a function ) [undone]

r
define a function that handles asserts  
define a function that handles 

