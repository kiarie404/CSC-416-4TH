- Redox is a general purpose OS written in Rust. By general purpose it means that it is built to interact with a variety of hardware and a wide range of applications. It is not purely application specific or hardware specific.

- One major goal of Redox is to provide an alternative to Linux on our computers : more modular and more secure than Linux. It should be able to run most Linux programs with only minimal modifications.
- It is more secure because it is written in Rust and it has a micro-kernel design. As for modularity, it uses a minimalistic syscall interface, it has an modular file system when compared to ZFS and it uses a microkernel architecture.

- Hobo OS on the other hand is not a general purpose OS. It might fall under Embedded operating system or Special-purpose operating system. (check under the Redox chatgpt chat)


- Redox has a unix-like micro-kernel architecture, its drivers run in the User-space: this is a security design choice which sacrifices performance in comparison to a monolithic OS that run eerything in kernel mode. In Redox, drivers and many application services can run in user mode, similar to user applications, and the system can restrict them so they can only access the resources they require for their designated purpose. If a driver fails or panics, it can be ignored or restarted with no impact on the rest of the system. A misbehaving piece of hardware might impact system performance or cause the loss of a service, but the kernel will continue to function and to provide whatever services remain available.
  
- Redox is Unix-like; It has a similar system architecture and the module naming conventions are significantly similar.
- Redox has a GUI called Orbital

- At this time, Redox supports:

    All x86-64 CPUs, and most i686 CPUs since the Pentium II.
    Graphics cards with VBE and/or GOP support (all Nvidia, Intel, and AMD cards from the past decade have this).
    AHCI, IDE, and NVMe disks.
    E1000 or RTL8168 network cards.
    Intel HDA and AC'97 audio controllers.
    Mouse and keyboard with PS/2 emulation.

- Redox modest compatibility with POSIX, allowing Redox to run many programs without porting. That way, new users of Redox will be able to use applications that they are used to with ease. Developers on the other hand do not have to build whole apps specifically for redox... they can tweak a posix compliant library to suit the redox OS.


- Some of the key features of POSIX-compliant operating systems and software include:

    A standardized file system hierarchy: POSIX-compliant systems have a standard directory structure that defines where system files, user files, and application files should be stored.

    A standardized shell interface: POSIX-compliant systems have a standard command-line interface (shell) that provides a consistent set of commands and utilities.

    POSIX-compliant APIs: POSIX defines a set of APIs for system calls, input/output, and other functions that are used by applications to interact with the operating system.

    Standardized utilities: POSIX-compliant systems include a set of standard utilities that perform common tasks, such as file manipulation and text processing.



Redox currently does not fully support the virtualization of other OSes.


##### Outcomes of Redox review
- Should I ue the Relibc [library](https://gitlab.redox-os.org/redox-os/relibc)?
- Should Hobo be POSIX compliant?
- Should I use the [Redox File system](https://gitlab.redox-os.org/redox-os/redoxfs)? Instead of building my own inferior File system?
- I will stick to the monolithic design because : 
  - it is easier to implement
  - our basic aim was to run wasm programs in kernel mode because they are assumed to be safe anyway
  - I was fascinated by the idea of Drivers running in user-space for the sake of security and making the OS more modular. Being modular is a huge plus in Embedded systems because it gives the developers the option of being minimalistic... no baggage drivers that may increase the overall vunerabilities and consume more space. Concerning this point, I am thinking of compiling the driver modules into webassembly modules that run in krnel mode. That way we may be assured of some security of the drivers.
- Should I use the microlithic architecture?