- Redox is a general purpose OS written in Rust. that is, they it is built to interact with a variety of hardware. It can also run and interact with a wide range of applications. It is not purely application specific or hardware specific.

- Hobo OS is not a general purpose OS. It might fall under Embedded operating system or Special-purpose operating system. (check under the Redox chatgpt chat)


- It has a unix-like micro-kernel architecture : ie It has a similar system architecture, the module naming conventions are significantly similar.

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

One goal of Redox is to provide an alternative to Linux on our computers. It should be able to run most Linux programs with only minimal modifications.


