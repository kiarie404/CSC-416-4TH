Before we transmit and receive, we have to set up the UART controller as follows:

    Set word length to 8-bits (LCR[1:0])
    Enable FIFOs (FCR[0])
    Enable receiver interrupts (IER[0])


why do we have to do the above 3 steps?
    The setup you described is a common configuration for a UART controller, and it has several important reasons.

    Set word length to 8-bits: This configuration sets the word length of the UART communication to 8 bits, which is the most commonly used word length for serial communication. This means that each character being transmitted or received will be 8 bits long. This is important to ensure that the data being transmitted or received is consistent and predictable.
    The word length describes how many bits the transmitter and receiver buffers may contain at once. The reason this is settable is because even though we can support up to 8-bits at once, the UART on the other side might not be able to.

    Enable FIFOs: The First-In-First-Out (FIFO) buffer is a hardware buffer that can store a certain number of characters before transmitting or receiving them. Enabling the FIFOs helps to improve the performance of the UART controller by allowing it to store multiple characters before transmitting or receiving them. This can help to reduce the number of interrupts that need to be generated and can also help to prevent data loss.

    Enable receiver interrupts (IER[0]): When a UART receives data, it stores the data in a receive buffer until the software is ready to read it. By enabling receiver interrupts, we tell the UART to generate an interrupt request to the CPU when the receive buffer is full, indicating that new data is available. This allows the software to read the data from the receive buffer in a timely manner, without having to constantly poll the UART for new data.

    Overall, these setup steps are important because they ensure that the UART is configured correctly for serial communication and that data is transmitted and received efficiently and reliably.

Is the UART still relevant... ?
    Yes, UART (Universal Asynchronous Receiver/Transmitter) is still a relevant technology in modern electronics and computer systems.

    While newer technologies like USB and Ethernet have largely replaced UART in some applications, UART is still commonly used for low-speed communication between devices. For example, many microcontrollers, sensors, and other embedded devices still use UART for communication due to its simplicity and low cost.

    UART is also useful in situations where a simple, point-to-point connection is needed between two devices. For example, UART can be used to communicate between a microcontroller and a display, or between a GPS module and a microcontroller.

    Additionally, many modern microcontrollers and processors include built-in UART interfaces, making it easy to implement UART communication in embedded systems.

    Overall, while UART may not be the most advanced or feature-rich communication protocol available, it still has its uses and remains a relevant technology in the field of electronics and computer engineering.

#### DUMP
Universal Asynchronous Reciever / Transmitter (UART)

A Universal Asynchronous Receiver/Transmitter (UART) is a type of communication interface that is used for serial communication between two devices. It is an integrated circuit that provides a simple and reliable way to transmit and receive data over a serial communication link.

A UART provides a way to transmit and receive data in a serial format, which means that the data is sent bit-by-bit over a single wire. The UART converts the parallel data from the device's internal bus into a serial format and then transmits it over the communication link. On the receiving end, the UART receives the serial data and converts it back into parallel format for use by the device.

UARTs are commonly used in embedded systems, personal computers, and other electronic devices that require serial communication. They are used to communicate with devices such as modems, GPS receivers, and other peripheral devices.

The communication speed of a UART is measured in bits per second (bps), and the baud rate determines the speed at which data is transmitted. The baud rate is set by the transmitting and receiving devices, and both devices must use the same baud rate for communication to occur.

UARTs can operate in full-duplex mode, where both devices can transmit and receive data simultaneously, or half-duplex mode, where only one device can transmit at a time.

Overall, UARTs provide a simple and reliable way to implement serial communication in electronic devices.
Kiarie 404
asynchronous

In computing and communication, asynchronous refers to a method of data transmission in which data is sent without a clock signal, meaning that the sending device and the receiving device do not need to be synchronized in terms of timing.

Asynchronous communication is also known as start-stop communication, because each piece of data is preceded by a start bit and followed by one or more stop bits, which identify the beginning and end of the data packet. This allows data to be transmitted at variable speeds, and devices can start transmitting or receiving data at any time, without waiting for a clock signal to synchronize with the other device.

Asynchronous communication is used in many different types of communication systems, including serial communication interfaces such as UARTs, as well as in networking protocols like Ethernet and USB. Asynchronous communication is particularly useful in situations where the data transmission rate varies, or where devices may be located far apart from one another, making clock synchronization difficult or impossible.
Kiarie 404
serial communication.

Serial communication is a method of data transmission in which data is sent one bit at a time over a single communication channel, such as a wire or fiber optic cable. In serial communication, data is transmitted in a sequential order, with each bit being sent one after the other, rather than in parallel.

Serial communication can be either synchronous or asynchronous. In synchronous serial communication, a clock signal is used to synchronize the transmitting and receiving devices, ensuring that each bit is transmitted at the correct time. In asynchronous serial communication, start and stop bits are used to indicate the beginning and end of each data packet, and the devices do not need to be synchronized.

Serial communication is widely used in a variety of applications, including data transmission between computers and peripherals such as printers, modems, and storage devices. It is also used in embedded systems, such as microcontrollers and sensors, as a simple and cost-effective method of communication.

Serial communication has several advantages over parallel communication, including lower cost, longer distance transmission, and the ability to use fewer communication lines. However, serial communication can be slower than parallel communication due to the sequential nature of the data transmission.
Kiarie 404
SO the work of a UART is to convert parallel bits into serial bits and vice versa?

Yes, that is correct. The primary function of a Universal Asynchronous Receiver/Transmitter (UART) is to convert parallel data from the device's internal bus into a serial format for transmission over a communication link, and to receive serial data and convert it back into parallel format for use by the device.

In other words, a UART acts as a bridge between a device's internal parallel bus and an external serial communication channel, such as a wire or fiber optic cable. It takes the parallel data that is stored in the device's memory or generated by the device's processing unit and converts it into a sequential stream of bits that can be transmitted over the serial communication link.

On the receiving end, the UART receives the serial data and converts it back into parallel format for use by the device. The UART is responsible for synchronizing the transmission and reception of data between the two devices, and it does so by controlling the timing of the start and stop bits that are used to mark the beginning and end of each data packet.
Kiarie 404
Rust does have raw pointers, but it doesn't have the volatile keyword. Instead, we use the pointers as objects and use the write_volatile and read_volatile members of Rust's raw pointers.   .... what is this "volatile" and why is it important?

In computer programming, the volatile keyword is used to indicate to the compiler that a variable's value may be changed by external factors that are not predictable or controllable by the program. This means that the value of a volatile variable should not be cached or optimized by the compiler, and that the variable must be read and written in its exact order of appearance in the code, without any reordering or optimization.

The reason that the volatile keyword is important is because it is often used in situations where hardware or other external factors can affect the behavior of a program. For example, in embedded systems or device drivers, there may be memory-mapped hardware registers that can be updated by external events, and the program needs to be able to read and write these registers as they are updated.

If a program were to optimize or reorder the access to these hardware registers, it could cause unexpected behavior or errors in the system. By marking the variables that represent these hardware registers as volatile, the programmer ensures that the compiler will not optimize or reorder the access to these registers, and that the program will behave as expected.

In Rust, the absence of the volatile keyword is due to the language's focus on safety and correctness. Rust's raw pointers provide the read_volatile and write_volatile methods to allow for access to hardware registers and other memory-mapped I/O, while still maintaining the safety and correctness guarantees of the language. This approach ensures that the program is safe and correct, while still providing the necessary functionality for low-level system programming.
Kiarie 404
how is the read_volatile in Rust safer than recular volatile

In Rust, the use of the read_volatile method of a raw pointer instead of the volatile keyword provides a safer way to access memory-mapped I/O and other hardware registers.

The read_volatile method in Rust is a safe interface for reading the value of a volatile variable. It takes a raw pointer as its argument, which provides a direct reference to the memory location where the variable is stored. By using a raw pointer, Rust can ensure that the memory access is safe and that the program cannot access memory outside the bounds of the variable or violate Rust's memory safety guarantees.

In addition, the read_volatile method provides a way to enforce correct memory ordering when accessing volatile variables. When accessing hardware registers, it is often important to read the register in a specific order, as different bits may represent different values or flags. The read_volatile method ensures that the order of memory accesses is correct, preventing any issues that could arise from incorrect ordering.

Overall, Rust's approach to volatile memory access using read_volatile is safer than traditional volatile usage because it provides a safe and controlled interface for accessing volatile memory. By providing a direct reference to the memory location of the variable and enforcing correct memory ordering, Rust avoids the undefined behavior that can arise from unchecked access to volatile memory.
Kiarie 404
Before we transmit and receive, we have to set up the UART controller as follows:

    Set word length to 8-bits (LCR[1:0])
    Enable FIFOs (FCR[0])
    Enable receiver interrupts (IER[0])
Why??

The setup steps you mentioned are common steps that are typically required to configure a UART controller for serial communication. Here's why each of these steps is important:

    Set word length to 8-bits (LCR[1:0]): This step sets the number of bits in each data frame that the UART will transmit or receive. A data frame is a single unit of data that is transmitted or received by the UART. By setting the word length to 8-bits, we ensure that each data frame consists of 8 bits of data, which is a common standard for many serial communication protocols.

    Enable FIFOs (FCR[0]): FIFO stands for "First-In, First-Out" and refers to a buffer that temporarily stores data before it is transmitted or received by the UART. By enabling the FIFO, we can store multiple data frames in the buffer before transmitting or receiving them, which can improve the efficiency of data transmission and reduce the overhead of transmitting or receiving individual data frames.

    Enable receiver interrupts (IER[0]): When a UART receives data, it stores the data in a receive buffer until the software is ready to read it. By enabling receiver interrupts, we tell the UART to generate an interrupt request to the CPU when the receive buffer is full, indicating that new data is available. This allows the software to read the data from the receive buffer in a timely manner, without having to constantly poll the UART for new data.

Overall, these setup steps are important because they ensure that the UART is configured correctly for serial communication and that data is transmitted and received efficiently and reliably.
Kiarie 404
What is baud rate

Baud rate refers to the number of signal or symbol changes that occur on a communication channel within a given time period. It is a measure of the speed of data transmission over a communication channel, typically expressed in bits per second (bps) or symbols per second (sps).

In serial communication, baud rate refers to the rate at which bits are transmitted or received over a communication channel. For example, if the baud rate is set to 9600 bps, the UART will transmit or receive 9600 bits of data per second. The baud rate must be set the same on both the transmitting and receiving sides of the communication channel in order to ensure that data is transmitted and received correctly.

The baud rate is a critical parameter in serial communication, as it determines the speed at which data is transmitted and received. A higher baud rate allows data to be transmitted and received more quickly, but it also requires a faster and more reliable communication channel to ensure that data is transmitted and received without errors. In contrast, a lower baud rate can accommodate a less reliable communication channel, but it will transmit and receive data more slowly.
