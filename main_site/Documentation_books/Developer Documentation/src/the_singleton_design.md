Software development is hard.  
When writing conmplex software that involves many modules interacting with each other... it becomes a good thing to create only one instance of a module that gets used by other modules.  
This reduces the complexity of having to manage many instances.  


Two principles :
1. Restrict the instantiation of a class to one object 
2. Ensures that there is a global point of access to that object. 


One of the many ways to implement Principle 1 :
- implement the class as a module with a well defined API. Uder that API, the costructor should be private. Just provide an "get_instance" function that calls the constructor in the background. In the Singleton pattern, the class provides a static method (often called getInstance()) that returns a single instance of the class. The method checks if an instance of the class has already been created, and if so, it returns that instance. If not, it creates a new instance and returns it.



Implementation In Rust :
preliminaries : 
- understand the ["static" keyword](https://doc.rust-lang.org/beta/std/keyword.static.html) in Rust
- have some knowledge on [std::sync](https://doc.rust-lang.org/beta/std/sync/index.html)


static keyword  
- static variables are variables that last during the entire program. THey are defined in memory, meaning you can access them as global variables. 
- Now that static variable memory point can be accessed through out the program by any program code, it means that mutating that memory point may cause thread unsafety. So anytime we mutate that memory point we do it in an unsafe block.
- Moreover, the value of the data made mutably static MUST implement the [sync trait](https://doc.rust-lang.org/beta/std/marker/trait.Sync.html)(A trait to show that a type is safe to share references between threads).


Under std::sync you can use the following objects... among many:  
  Mutex: Mutual Exclusion mechanism, which ensures that at most one thread at a time is able to access some data.
  Once: Used for a thread-safe, one-time global initialization routine
  OnceLock: Used for thread-safe, one-time initialization of a global variable.

Example :
```rust
use std::sync::{Mutex, Once};

pub struct UartDriver {
    // ... UART driver state goes here ...
}

impl UartDriver {
    // Private constructor to prevent other code from creating instances of UartDriver
    fn new() -> UartDriver {
        UartDriver {
            // ... Initialize the UART driver state here ...
        }
    }

    // Static method that returns the singleton instance of UartDriver
    pub fn get_instance() -> &'static Mutex<UartDriver> {
        static mut INSTANCE: *const Mutex<UartDriver> = 0 as *const Mutex<UartDriver>;
        static ONCE: Once = Once::new();

        unsafe {
            ONCE.call_once(|| {
                // Create the singleton instance of UartDriver using a Mutex to allow safe concurrency
                let mutex = Mutex::new(UartDriver::new());
                INSTANCE = std::mem::transmute(Box::new(mutex));
            });

            &*INSTANCE
        }
    }

    // ... Methods for interacting with the UART go here ...
}
```




##### Implementation

Source : [this blog](https://refactoring.guru/design-patterns/singleton)