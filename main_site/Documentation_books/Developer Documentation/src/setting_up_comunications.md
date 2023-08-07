# Setting Up Communications

References :   
    - [Chapter 3 : How to write Drivers for Peripherals](https://docs.rust-embedded.org/book/peripherals/index.html)    
    - [Communications : the UART Driver](https://osblog.stephenmarz.com/ch2.html)      
    - [The UART specifications](https://www.lammertbies.nl/comm/info/serial-uart)      
    - [The UART Datasheet](http://caro.su/msx/ocm_de1/16550.pdf)     

Our OS will communicate with peripherals. Peripherals are external devices...external cirtuits... things that are not originally part of the Motherboard.   
In this chapter, the peripherals that we will be attaching will be the ***console*** for output and the ***keyboard*** for input.     
The connection between the microcontroller and both of these peripherals is a serial connection.   




# I2C
   - packet-wise, has start and stop bits. Not streaming
   - 
# SPI
   - continuous, not packet-wise. data does not have  start and stop bits
   - data can be transferred without interruption. Any number of bits can be sent or received in a continuous stream.... NOT packetwise
   - 
# UART
   - packet-wise, has start and stop bits. Not streaming
   - 