# No-std testing    

references :    
1. [Normal Testing](https://doc.rust-lang.org/rust-by-example/testing.html) 
2. [No_std Testing](https://os.phil-opp.com/testing/)
3. [Extra](https://ferrous-systems.com/blog/test-embedded-app/)


Rust has a built in test framework. It helps in automating unit tests and integration tests.   
But this framework is dependent on a library called "test". "test" is dependent on the std library.  
Since we are working in a no-std environment, the default test-framework used by the rust compiler becomes unusable.    

Some good news: Rust compiler gives you an option to use a custom test framework if you do not prefer to use the default std-dependent framework.  
You have to activate the unstable feature called : [custom-test-frameworks](https://doc.rust-lang.org/unstable-book/language-features/custom-test-frameworks.html)  
The disadvantage compared to the default test framework is that many advanced features, such as should_panic tests, are not available. Instead, it is up to the custom implementation to provide such features itself if needed

The feature requires you to Define a runner function that takes in all of the test functions collected in the form as an array and executes them to your specification.    
As seen, you can specify how the tests are run, which tests get to run, and how the test results get displayed. You are free to code the behavior of your test handler.   


## Steps to setup no-std tests 
1. Activate the feature across the entire crate by adding this attribute to the root of the crate (in our case, main.rs):   
```rust
// in main.rs or lib.rs
#![feature(custom_test_frameworks)]
``` 
2. Specify the path of the test runner function, describe this path crate-wise. To be specific, state the test runner in the root of the crate. The test runner function can be code found in another module. You can go wild   
```rust
// in main.rs
#![feature(custom_test_frameworks)]
#![test_runner(crate::path_to::your::runner_function::custom_function)]
``` 
3. Set up display system. You can use your own machine drivers or an external console. Just find anway for print and println to work.
   - Add println macro to your crate.
   - Define a good panic handler function with println capabilities

4. Define the runner function. as follows :
```rust
#[cfg(test)] // make cargo test consider this module
fn custom_function( bunch_of_tests_in_an_array: &[&dyn Fn()]){ // funtion accepts an array of functions that have been tagged with the #[test_case] attribute
    println!("HAndling {} tests", bunch_of_tests.len());
}
```
5. The test framework exports its entry point function as main(). But in this environmet, main is ignored. You need to intergrate the entry point of the test framework as part your current entry point.  
So we make the test framework define its entry point with a name that we specify. Like this :
 
```rust
#![feature(custom_test_frameworks)]
#![test_runner(crate::path_to::your::runner_function::custom_function)]
#![reexport_test_harness_main = "custom_name_of_entry"]
```
6. call the entry_point function under your current program entry point. For Example
```rust
// in main.rs OR lib.rs
#![no_std]
#![no_main]
#![feature(panic_info_message)]
#![feature(custom_test_frameworks)]
#![test_runner(crate::handle_tests)]
#![reexport_test_harness_main = "custom_name_of_entry"]

mod stdout;  // For the println and print macros
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
    println!("Handling {} tests", bunch_of_tests.len());
}

#[cfg(test)]
fn trash_test_function(){
    println!("I am a trashy test... Life as trash has no pressure. ");
}

```
## How to print test results
- define custom assert
- use procedural macros to print function name/ For now just write custom messages as demonsstrated in the RustConcepts place   

## How to run tests module-wise
The main runner function take in ALL functions that have the #[test_case] attribute.  
What if you do not want to run all tests? What if you just want to run the tests of a certain module only?  

**Solution**    
Let us say that you have a module called "tv". The path to this module is "crate::tv".  
"tv" module has an inner test module that declares test functions: "crate::tv::tests". The tests are unit tests.  

the tv module has 3 functions :
- switch_on()
- switch_off()
- change_channel()

The unit test for the tv module has 3 tests: 
- test_switch_on()
- test_switch_off()
- test_change_channel()

Here is the source file for the tests
```rust 
// in src/tv/tests.rs

#[test_case]
fn test_switch_on(){  /* code */ }

#[test_case]
fn test_switch_off(){  /* code */ }

#[test_case]
fn test_change_channel(){  /* code */ }
```

If you run "cargo test" command, all three test functions will run. 
You then build a switch, as demonstrated below. We have also removed the #[test_case] attribute to the many functions. Now we have one place to control the tests of a single module:

```rust 
// in src/tv/tests.rs

#[test_case]
fn switch(){
  test_switch_on()
  test_switch_off()  // comenting a function out disables it
  test_change_channel()
}

fn test_switch_on(){  /* code */ }

fn test_switch_off(){  /* code */ }

fn test_change_channel(){  /* code */ }
```

To disable all tests of this module, remove the #[test_case] attribute from the switch.  
To disable a particular test, comment it out inside the switch function
 

# No-std Integration Tests
[undone] : Just Document the setup, not the actual tests. The actual tests specific to this project will be covered in the next chapters

An integration uses the crate an an external dependency. It depends on the std-dependent test framework. Since we cannot afford using that framework in a no-std environment, we manually set up complete new rust projects that depend on the "hobo-os" crate.  

Steps to create an integration test binary :    
1. Next to the "Hobo-OS" directory, create a new binary crate using the "cargo new" command. eg App
2. In the cargo.toml file, add the kernel as a dependency
3. Copy the following files and folders from the Kernel folder into the App Folder.
   1. /.cargo
   2. hdd.dsk
   3. /src/lds
4. Paste the template below into your main.rs file. Build on it however you want
```rust
#![no_std]  // we will no depend on Rust Standard Library and Libc
#![no_main] // we will define our own entry point sequence using the linker + Bootloader
#![feature(panic_info_message)]

use core::panic::PanicInfo;
use core::arch::asm;

// macros
use hobo_os::{print, println};
// libraries
use hobo_os::drivers;
use hobo_os::stdout;

// defining the entry point function
// kinit returns the satp value .  
// this value gets used to update the satp register before executing kmain
#[no_mangle]
pub extern "C" fn kinit () {
    println!("I am in Machine mode...");
}

#[no_mangle]
pub extern "C" fn kmain() -> (){
    // Show that we are in supervisor mode
    println!("Hello world, I am in supervisor mode!!!");
    
    println!("hahaha, I am going to shut down.... see you later.");
    return ();
}



// defining the function that will always get called after a panic
#[panic_handler]
fn panic_handler (panic_info: &PanicInfo) -> !{
    // make the current CPU_core sleep endlessly and wait for Interrupt
    let panic_location = panic_info.location().unwrap();
    let panic_message = panic_info.message().unwrap();
    // println!("Panic occured : in file : {}, line {}, with the message : {:?}",
    //                                              panic_location.file(), panic_location.line(), panic_message);
    loop {
       unsafe { asm!("wfi");  }
    }
}

```
   
