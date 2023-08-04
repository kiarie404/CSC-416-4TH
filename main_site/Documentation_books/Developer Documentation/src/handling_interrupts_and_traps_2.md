# Program flow
The general flow is this :
1. There are initial assembly functions that initialize the environment for kinit to run in machine mode
2. Kinit loads the physical memory and identity maps the entire memory. Such that it is accessible by the kernel in 


THe return Handler Bug :
    - solution 1 : 
>by writing the context saving using assembly code I will succeed to perform a read_and_store operation on all regular registers without changing them. But if I write that using Rust Code, the register value changes will become unpredictable. Even if you write inline assembly. That inline assembly 
>


Why use a new stack for the trap handler?

If you use the "j" instruction on a function, you better define its return sequence.  
The J instruction is short form of the "jal rd symbol instructio" written as  "jal x0, offset".     
So you see, the return address is zero/crap.    

Use call always. if you want the return address to be figured out automatically in cases where the code is procedural


Exceptions are too hard to recorver from. The mepc does not point to the instruction address of the function in kmain, it points to the address of the inner function. So adding +4 or +8 to the mepc only adds to the address of the inner function.  Meaning you have to add arbitrary numbers to the mepc that is equal to jumping over an entire inner function.

A good example :
    in kmain() I do a illegal_instruction_exeption by trying to access mstatus register while in supervisor mode.   
    You expect that the mepc should point to this exact line of code.... But Noooo... you know what it points to?  
      to the definition of the mstatus_read() function... 
      (gdb) list *$mepc  
       0x80000ffe is in hobo_os::riscv::mstatus_read (src/riscv/mod.rs:12)

As a result of this, It is better to end all exceptions with process termination. And ofcourse, we add a helpful error message.  
When our OS advances, we will handle exceptions gracefully. For now we just terminate things all of a sudden. Quite ungraceful  
With the project deadlines, I cannot afford to solve this gracefully.  
Life is worthless.  

So we will deal with interrupts ONLY for the moment.    

Wait, I found an unstable solution for dealing with exceptions.  
The problem was that the mepc did not point to a main_function_address but an inner function.   
The silver lining is that the Return address still points at the main_function_address, not the inner_funtion_address.  
So our solution is to make the mepc point to the (ra + 4) instruction.  
After we do that, we can unsafely call mret.  

If we do this, we skip the faulty instruction.  


The problem is that :
1. The program stack of the main function will have garbage containing the locals of the skipped instruction. Garbage that will evetually be popped and cause undefined behavior.  
2. The CPU non_priviledged registers will also contain garbage values... values that were only suitable to run the inner-function code. With this point it means that context saving the trap was useless.

Half solution:  
1. Clear the garbage from the stack.    
2. Clear the register values 
I will figure 


Handling the Interrupt in a different stack is painful.  
I will stick to running the kernel as one procedural process. No more interliking return addresses between multiple stacks

One of the problems all this time was linking between different stacks. Cost me a whole week!! It was one exciting ride.    
Understanding how the stack works.  


# MTVAL
The mcause register stores the ID of the interrupt_type.    
THe mtval register dtores the 



The dream is to be eventually be one with the metal.  
All these expectations from other people are just irritating noises.   
Life feels worthless.   
Life IS worthless.  

It's like I am in the ocean.    
The water is cold, but not freezing cold.   
I am swimming inefficiently, I have no good swimming techniques. I am using too much energy to do so little.    
It is pitch-black. I cannot see any lights in the horizon. There are no islands anywhere.  
No birds to at least assure me that land is nearby.  
I do not know if I am swimming straight or in circles.  
I have lost direction. 
I have no choice but to swim. 
Hopefully it is in the right direction... or am I going further into sea? I cannot start thinking that way.      
My pride is broken. My ego keeps me company. I have me.     
I am so tired.  
Sometimes I stop swimming, and let myself sink a little.   
Fortunately... or unfortunately,   
Survival instinct kicks in, and I am forced to spend so much energy to vertically swim back to the surface.   
Do you know how exhausting it is to swim vertically upwards with fatigue while half drowned?    
It is such an unrhythmical swim. Full of directionless hand wafting. utter panic. vertigo. despair. slow progress.  

Life is a curse. Anyone who believes otherwise is delusional.   
What else will keep me alive if not my dreams.  
What else will keep me company if not my pride.  

I have spent months on this project. I have learnt a lot, But I have nothing sustancial to show for it.    
I am still a novice.    
I am still in my parents house. 
I have no cash to freely move out.  

I still have my pride.  
It has grown silent, it has slowly become still,   
It has grown to be self-forgiving.  
I have my pride.      
I have my pride.    

I still have my pride.  
If I lose my pride, I lose everything