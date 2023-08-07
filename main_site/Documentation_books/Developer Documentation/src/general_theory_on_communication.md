# General Theory on communication

## Serial and Parallel Communication
[undone] : define serial and parallel communication. Explain this info important?

Inside the motherboard circuit or the peripherals circuits, the internal components are connected using buses(parallel connections).   
But the cables connecting these independent circuits are typically serial connections mostly because of the cost of creating cables that are wide.  
Another reason is the simplicity in configuration using serial lines

[undone] : Image demonstrating the advantages/disadvantages of using parralel OR serial connections 

| Parallel Connection                                                                                                                                                                                                          | Serial Connection                                                                                                    |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| parallel connection is expensive                                                                                                                                                                                             | serial connection is less expensive                                                                                  |
| parallel connection is fast                                                                                                                                                                                                  | serial connection is less fast                                                                                       |
| parallel connection is hard to configure perfectly (you still need some agreed upon level of lines : You are boiling x number of lines to y lines today, tomorrow you may need to boil it down to z lines -- that is hectic) | serial conection is more universal in its configuration (you are boiling down x number of lines to 1 line -- ALWAYS) |
|                                                                                                                                                                                                                              |                                                                                                                      |
|                                                                                                                                                                                                                              |                                                                                                                      |



#### Parallel connection
parallel connection is expensive    
parallel connection is fast      
parallel connection is hard to configure perfectly (you still need some agreed upon level of lines : You are boiling x number of lines to y lines today, tomorrow you may need to boil it down to z lines -- that is hectic)

#### Serial Connection
serial connection is less expensive  
serial connection is less fast   
serial conection is more universal in its configuration (you are boiling down x number of lines to 1 line -- ALWAYS)  


## Theory of parallel to serial conversion
Before we discuss the actual Drivers and Registers, let's discuss the theory of parallel-to-serial conversion.  

1. On the Sender's side
The serializer gets configured by the host system. The baud rate is set, the size of a data frame is set and interrupt handling is configured.
The serializer hardware receives parallel input... let's say 8 bits.  
The serializer sequentially pushes each bit of that parallel input into a shift register. Whether it begins from the Most Significant bit or the LSB is up to the specifications of the communication protocol being used.    
The serializer packages the shift register bits as a data frame, with a startbit, a stop-bit and maybe a parity bit for error checking.   
The serializer sequentially pushes the bits out of the shift register and into the transmission channel.  
The way it pushes the bits into the line can be either FIFO or FILO.  
The rate at which it pushes the bits into the transmission line is called the Baud Rate. It is measured in bits per second. In this case, baud rate is the same as bit rate because each bit change equals a bit transmission.  

2 On the receiver's end
It is assumed that the receiver has the same configurations as the sender: same baud rate, same data_frame size.  
The deserializer receives the serial data frame.   
checks the parity bit for error detection  


## Communication Protocols
A protocol is a set of rules or specifications on how to do something.  
In this case, the protocols we are talking about are protocols about converting parallel data signals into a serial signal.  
There are many protocols, but our focus will be on the UART 'protocol'. The reason the name protocol is in quotes is because there is no standard UART protocol. The specifications implemented vary depending the UART devices and driver settings.    

#### Why are we not using the USB driver/protocol?  
The USB also does the conversion of parallel signals to serial signals. The USB has higher transfer speeds than the UART connection. The USB can do 20 Gbps while the UART does around 1 Mbps!   
However, we will use the UART because of two reasons :  
1. Its simplicity in configuration. The UART is easier to configure when compared to the USB. This is a learning project, we cannot afford to delve into the complex nature of USBs.  
2. It uses less power than the USB. The project is targeting embedded systems; the less power our machine consumes, the better. 

We trade-off the speed of the USB for the simplicity and power-efficiency of the UART.  


