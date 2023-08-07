# Timer_interrupt

In the world of traps, you can experience either an interrupt or an exception.  
There are 3 kinds of interrupts. 
    1. A timer interrupt
    2. A software interrupt
    3. An external interrupt

Under this section, we discuss the timer interrupt.  
A timer interrupt is an interrupt coming from the hardware clock of the motherboard. 

You can set the frequency in which the clock continuously interrupts the CPU.    
You can also set an alarm.  

**Why would you set the clock to interrupt the CPU at a known frequency?**  
time slicing. Please use chatgpt here future reader. I am sorry. I am waaaay behind irritating deadlines. [undone]

**Why would you set an alarm?**  
[undone]

# The CLINT (Core Local Interruptor)

The CLINT is a piece of clock hardware that can interrupt the CPU based on countdowns set.  
The CLINT can only be accessed while in either of the priviledged modes (Supervisor and Machine Modes)  

The primary purpose of the CLINT is to provide timer interrupts to the processor. It consists of a programmable timer, which can be set to generate interrupts at specific intervals.  

The CLINT exposes its registers in the I/O dedicated memory.    
It exposes many registers, I do not know most of them. I will concentrate on two registers only :
1. mtime register
2. mtimecmp register

Every implementation of a CLINT has a specific frequency measured in Hartz. For example 10_000_000 Hz.  
The Frequency is the rate at which the value in the mtime register gets incremented by 1.  
For example. If the value in the mtime register is 12 and the frequency of the CLINT is 10_000_000 Hz. It means that after one second, the value in the mtime would be 10_000_012. After 30 seconds it would have been 30_000_012.  

If we use fancy explanations : it means that the mtime register will be incremented by 1 every 1/10,000,000th of a second (1/10 μs or 100 ns) IF the frequency of the CLINT is 10_000_000 Hz.   

Now the mtime register is 64 bits long. meaning that the highest value it can contain is 2^64. If the value passes this limit, it rolls over... and starts counting from zero again. So watch out for that.  

The mtimecmp register also stores a 64 bit value. Once the value in the mtime register exceeds the value in mtimecmp register, a timer interrupt is sent to the CPU. As long as the mtime value is bigger that mtimecmp, the timer will keep on sending interrupts to the CPU. It is up to the interrupt handler to set the mtimecmp to a higher value. If you do not set the mtimecmp to a higher vakue, yuor CPU will get bombarded with a stream of timer interrupts; You would have achieved something like a denial of service attack.  And you wonder why the mtimecmp and mtime registers are priviledged registers.  

A timer interrupt can be classified as A supervisor_timer_interrupt or a machine_timer_interrupt. Both interrupts come from the CLINT. a timer interrupt gets called supervisor_timer_interrupt if the CPU was in supervisor mode when the interrupt happened.  
The timer interrupt gets called a machine_timer_interrupt if the CPU was in machine mode when it received the timer interrupt signal.   

In our Qemu Virtual Machine, 
    - The mtime register is at 0x0200_bff8
    - The mtimecmp is at 0x0200_4000
    - The frequency is at 10_000_000 Hz


As long as the mtime value is 

