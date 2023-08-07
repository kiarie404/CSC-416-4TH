# More About the UART


You need to configure the UART devices on both the receiver and sender.

Configuration :
1. Set the baudrate : communicating devices need to be within 10% range of difference
2. Are we going to include the parity bit in our transmissions?
3. How are the Interrupts handled? 

## Configuring the Baud rate:
The Baud Rate formula is as follows :  
Divisor = UART_device_clock_frequency / ( Baud_rate x Prescaler_Division_value)     

where :
- Divisor is a 16 bit value whose first 8 bits get stored in the DLM register and last 8 bits get stored in the DLL register. If we do not set the DLL and DLM bit, the Divisor value is assumed to be  65,536 (2^16).
- UART_device_clock_frequency is the clock speed of the particular UART implementation. eg 16MHz
- The Baud_rate is the rate at which data transfers, this is what we are trying to calulate.
- The Prescaler_Division_value is found in the Prescaler_Division register. It is represented using 4 bits, its value ranges from 1 to 16. If we do not set the prescaler division value, 16 is assumed to be the default. We are okay with 16

We will use this registers to :     
1. Initialize the communication between the 2 devices
2. fetch and write data to the communication buffers


Now, there is a conflict, The DLL bit occupies the same space that the THR and RHR occupy.  
This problem is solved by doing the baud rate configuration once. After that the space is left for RHR and THR.  
That space is only usable for the DLL only if the DLAB bit is set to one. When the DLAB bit is set to 1, the UART realized that it is configuration time and dedicates the register to store the DLL bit before it does the bitrate calculation.  

So the DLAB bit acts as a switch  :
When the DLAB bit = 1 then...
1. register 000 stores the DLL value
2. register 000 stores the DLM value
3. register 101 stores the Prescaler value

Once the baud rate configuration is complete, the DLAB bit is set to 0, and the register space that was used for DLL and DLM becomes available for accessing the THR and RHR, respectively. The UART controller switches back to data transmission/reception mode.


## Parity  Bit
No we do not care about this. It is important, but we are in a virtual environment, why would bits get lost in transmission? We are in a perfect world.  

## Interrupt handling
THe UART is conected to the CPU via the PLIC. The UART send out 5 kinds of interrupts to the CPU.  
1. Receiver Data Available Interrupt (RDA): This interrupt is triggered when data is received and available in the receiver buffer (RBR/RHR) to be read by the CPU.

2. Transmitter Holding Register Empty Interrupt (THRE): This interrupt is triggered when the transmitter buffer (THR) is empty and ready to accept new data to be transmitted.

4. Receiver Line Status Interrupt (RLS): This interrupt is triggered when a line status error occurs during data reception, such as framing errors, parity errors, or overrun errors.

5. Modem Status Interrupt (MS): This interrupt is triggered when there are changes in the modem status signals, such as changes in the CTS (Clear To Send) or DSR (Data Set Ready) signals.

6. Receiver Time-Out Interrupt (RTO): This interrupt is triggered when the receiver is idle for a specified duration, indicating the end of a data transmission.

In our case, We only care about the first 2 interrupts.  

To enable the UART device to be able to send interrupts to the CPU, we have to tweak the bits of the Interrupt enable register.     

