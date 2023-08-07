# Debugging in Rust
[references]
1. Mastering Rust - Second Edition by Rahul Sharma, Vesa Kaihlavirta (chapter 17: Debugging)

## Print statements are not enough.    
1. Print statements cannot be used to display everything. For example, they cannot easily show how register values change.   
They can only show results after a bunch of code has executed... they don't show the exact internal changes of those lines of code.  

2. Some environments do not have print capability, for example bare metal environments.  
3. What would you do if you are debugging the "print system" itself?  

## Pre
- What is a debugger
- Examples of known debuggers (gdb, lldb)

**What is a bug**   
An unexpected program behavior. "Oh! Why is this addition function actually doing multiplication?"

**What is Debugging?**  
Removing bugs from the program. By systematically understanding the cause and effect across your code. Hoping you will notice the cause that is causing a bug.  

**What is a Debugger?**  
There are multiple ways of debugging. For example, You can use print statements, a REPL, Dumbing down the problem to a smaller scale, taking a shower and touching grass - hoping that a solution will finally hit you...  point is, you can figure out what the bug is using many methods.  

A debugger is a tool that allows you to place symbols across the compile binary. You can then inspect the internal state of the program at runtime.  


"Debuggers are programs that can inspect a program's internal state at runtime, provided that the program has been compiled – debug symbols included"

**Problem** : You cannot use a debugger on a program that has a compile-time error.  


A compiled program or object file is a sequence of zeros and ones that has no mapping of the original source from which it was compiled. To enable a program to be inspected by a debugger, we need to map the compiled binary instructions to the source file somehow.  

When you compile your Rust code with "cargo build --release" tag, it produces a binary file with NO debug symbols attached.   
When you compile your Rust code with "cargo build --debug" tag, it produces a binary file with debug symbols attached.  
You can however make the --release binary include Debug symbols by changing the cargo.toml configuration as follows :
```toml
[profile.release]
debug = true
```

You can set breakpoints : The program pauses when it reaches a breakpoint, you can then proceed to inspect the state of the program.    
You can set watch_points : The program stops/notifies you when a variable is read or written to.    

gdb and lldg can get the work done. But they are not Rust-specific. THeir output is not pretty, too much detail.  
For example, poor display of traits and structs.    
So there are rust-wrappers on top of Gdb and LLdb. They are mostly display wrappers. Meaning that vanilla gdb and lldb will still work fine.    

## Example 
- refer to the .Sandbox/Debugging code

### Example Steps
1. Plan your debugging session
   1. inspect main() segemnts
   2. inspect a particular function within main3. 
2. Set breaking points at certain locations

qemu-system-riscv64 -machine virt -cpu rv64 -smp 4 -m 128M -device virtio-blk-device,drive=attic -drive if=none,format=raw,file=hdd.dsk,id=attic -nographic -bios none -kernel target/riscv64gc-unknown-none-elf/debug/hobo_os -s -S


Stupid steps :
- Compile with debug symbols (explicitly) 
  - modify cargo.toml (both release and debug)
  - go with debug for just in case.
  - go with release for surety
  - If you get the (??), it means there are no debug symbols
  - 
- Enable Qemu debugging and pause_start kernel
  - add the -s -S .
  - modify the runner : qemu-system-riscv64 -machine virt -cpu rv64 -smp 4 -m 128M -device virtio-blk-device,drive=attic -drive if=none,format=raw,file=hdd.dsk,id=attic -nographic -bios none -kernel target/riscv64gc-unknown-none-elf/debug/hobo_os -s -S

    ```bash
    qemu-system-riscv64 -machine virt -cpu rv64 -smp 4 -m 128M -device virtio-blk-device,drive=attic -drive if=none,format=raw,file=hdd.dsk,id=attic -nographic -bios none -kernel target/riscv64gc-unknown-none-elf/debug/hobo_os -s -S
    ```

- start cross-debugging
  - use gdb-multiarch
  - specify the architecture you are debugging 

   Steps:
      1. gdb-multiarch -q -tui -ex "set architecture riscv:rv64"
      2. load the binary file : "(gdb) target/riscv64gc-unknown-none-elf/release/hobo_os"
      3. connect to the paused Qemu : "(gdb) target remote :1234"
   
- Just set the brakpoint as you wanted. Preferably at start and "continue" The source code will appear on screen
- for example :
```bash
break _start
break kinit
continue    
```
Continue Resumes execution until the next breakpoint.  

Here are other useful commands :  
To proceed with debugging, you can use GDB commands to set breakpoints, step through the code, and examine memory and registers. Here are a few common GDB commands to get you started:

    break function_name: Set a breakpoint at the specified function.
    break filename:linenumber: Set a breakpoint at the specified line in the source code.
    step: Execute the current line of code and stop at the first instruction of any called function.
    next: Execute the current line of code and stop at the next line in the current function.
    print variable_name: Print the value of the specified variable.
    info registers: Display the values of all CPU registers.

Errors with dealing with files that are not procedural in the call-way: eg _start --> some_boot_functions --> kinit --> kinit returns to ---> "initialize_environment_for_kmain" --> kmain

You get errors like :
1. Single stepping until exit from function _initialize_environment_for_kmain, which has no line number information.
   This means that the code that the debugger is currenly referencing a function called "_initialize_environment_for_kmain". And this code does not have line information
   This error may occur because of two reasons :
      1. The debugger cannot find the debug symbol table : This can be solved by adding the debug sybol table "debug = true" in the build configuration.
      2. The code is not part of the Source Code scope. The code is not part of the Source Code because it is an object file dependency. And those object file dependencies were not compiled with debug symbol tables. So the debugger does not have line information for them. 
      This might happen if you step on code such as boot_code, external libraries, initialization routines.  
      In our case, This happens because the "_initialize_environment_for_kmain" was written as assembly language that glued together the source_code for kmain and kinit.  
      This assembly code was compiled as a dependecy file.  

      To solve this, We can decide to use step and next commands ONLY when we are within the source code scope. You will know that you are in the source code scope if the source code gets displayed on screen. The moment you reach the end of a function, call "continue" comand so that you can cruise over the 3rd party code.  

      You can also decide to make ALL CODE to become Rust_written code. That way, assembly files that act as external files do not get to exist. You can assure this by making all assembly be "inline assembly".  

      You cn also decide to compile the external assembly files with debug information attached. I do not know how to do this.   
2. fdfd




On this day : 2/7/2023  
The Debugger saved my life. I will be eternally grateful to the creators of gbd and gdb muilti-arch   
You saved my life.  
Debugging is the BEST!!  
I will worship GDB-multiarch for the rest of my life. 

Especially the command : "info registers" --- I LOVE YOUUU. HA HA HA . 


As for tutorial :
See this chatgpt talk :  https://chat.openai.com/share/feda48a4-27ce-4563-b0d4-73047257256f


more commands:
1. info breakpoints --get info about the set breakpoints
2. break file:line_number

If you have the address of an instruction, you can get info about the source code of that specific instruction  using the list command: For example
```bash
(gdb) list *0x8000035e
0x8000035e is in hobo_os::interrupt_and_exception_handling::rust_trap (src/interrupt_and_exception_handling/mod.rs:65).
(gdb) list 0x8000035e
Function "0x8000035e" not defined.

```


To print the symbols that are in the current context (i.e., the current scope) while debugging with GDB, you can use the info locals and info args commands. These commands show local variables and function arguments, respectively,



If you call a function in assembly. Make sure the source code of that funtion has a return function  
In hindsight, if you are trying to modify the return address in rust code, it is better to use jump instruction instead of a call instruction.

## printing registers
- To print all non-priviledge registers : "info registers"
- To print a specific register (both priviledge and unpriviledged) : "print $mstatus", "print $ra" 

## Printing Memory values
To print the value stored at a specific memory address while using GDB (GNU Debugger), you can use the x (examine) command. The syntax for the x command is as follows:


x/[n][fu] address

Where:

    n is an optional count representing the number of units to display. If omitted, it defaults to 1.
    f is an optional format specifier to control the display format of the data.
    u is an optional unit size specifier (b for bytes, h for halfwords, w for words, g for giant words).

For example : 
x/2h 0x12345678

This will print two consecutive halfwords (16 bits each) starting from the memory address 0x12345678.