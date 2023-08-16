


# Paper

- [1]: MIT's Center for Bits and Atoms About Page : http://cba.mit.edu/about/index.html
- [2]: https://helda.helsinki.fi Isomorphic Internet of Things Architectures With Web Technologies By Mikkonen, Tomm
- [4]: Wasmachine: Bring IoT up to Speed with A WebAssembly OS By Elliott Wen and Gerald Weber
- [5]: Github List of Runtimes
- [6]: https://hacks.mozilla.org/2019/03/standardizing-wasi-a-webassembly-system-interface/
- [7]: Official WASi website : https://wasi.dev/
- [8]: WASI document Guide : https://github.com/bytecodealliance/wasmtime/blob/main/docs/WASI-documents.md
- [9]: Official Tock Website
- [10]: Tock Paper
- [11]: Own Docs 
- [12]: https://github.com/riscv-collab/riscv-gnu-toolchain
- [13]: Stephen Marz block driver
- 








































#### Web assembly 
1. 
2. Lin Clarks blogs :
   - [Standardizing WASI: A system interface to run WebAssembly outside the web](https://hacks.mozilla.org/2019/03/standardizing-wasi-a-webassembly-system-interface/)
   - [Announcing the Bytecode Alliance: Building a secure by default, composable future for WebAssembly](https://hacks.mozilla.org/2019/11/announcing-the-bytecode-alliance/)
   - [WebAssembly Interface Types: Interoperate with All the Things!](https://hacks.mozilla.org/2019/08/webassembly-interface-types/)
3. [Philip Opperman' blog on building an OS with Rust on x86 cpu](https://os.phil-opp.com/)
4. [Web Assembly paper on security](https://www.usenix.org/system/files/sec20-lehmann.pdf)





#### Possible for Rust+riscv
- [XV6 book rev 1](https://pdos.csail.mit.edu/6.828/2020/xv6/book-riscv-rev1.pdf) but find rev3
- https://github.com/Ko-oK-OS/xv6-rust
- [rcore tutorial v3](http://rcore-os.cn/rCore-Tutorial-Book-v3/index.html) (Rust + riscv)
- [Implementation of xv6-rust](https://github.com/Jaic1/xv6-riscv-rust)
- [Another Implementation of xv6-rust - MIT ](https://github.com/mit-pdos/xv6-riscv)
- [risc-v talk](https://www.infoq.com/presentations/risc-v-future/)
- [Stephen Marz Blog](http://osblog.stephenmarz.com/index.html)
- [rcore v2](http://rcore-os.cn/rCore_tutorial_doc/)

##### Risc v
- [Why adding extensions to x86 is a headache](https://www.anandtech.com/show/3593)
- [Risc V official specifications](https://riscv.org/technical/specifications/)
- [Simple down to earth Blog](https://danielmangum.com/categories/risc-v-bytes/)
- [Riscv down to earth Tutorial](https://smist08.wordpress.com/2019/09/06/introducing-risc-v/)
- [Riscv Control and Status Registers](https://book.rvemu.app/hardware-components/03-csrs.html)
- [Riscv manual - by SHakti deelopment Team](https://shakti.org.in/docs/risc-v-asm-manual.pdf)

##### General
- [How to write Linker scripts](https://sourceware.org/binutils/docs/ld/Scripts.html)

##### Redox
- [Redox Official Book](https://doc.redox-os.org/book/ch00-00-introduction.html) 
- [Redox Official Website](https://www.redox-os.org/)


##### Sel4
- [proving things](https://twanvl.nl/blog/agda/sorting)
- [Sel4 Official Website](https://sel4.systems/)

#### Tock OS
- [Official website design description](https://www.tockos.org/documentation/design)
- [why Rust Tock Papers](http://www.amitlevy.com/papers/tock-plos2015.pdf)

#### Theseus
- [THeseus Book](https://www.theseus-os.com/Theseus/book/index.html)

#### Why Rust
- [Video by Kevin Boos](https://youtu.be/mmJiwscpB4o)
- [Slides by Kevin Boos](https://docs.google.com/presentation/d/e/2PACX-1vQYomAnfTNucuCqYgNkPaxpIdrhPxil9Qzle_6-xd7TYfdEBlgML0B3vztdNC2odwc25dLzW3XsithZ/pub?start=false&loop=false)
- [Report on using Rust to write an OS](https://scialex.github.io/reenix.pdf)

#### Memory safety
- What is memory safety? - [Mozilla Hacks](https://hacks.mozilla.org/2019/01/fearless-security-memory-safety/)
- What is memory safety? and why is it important? - [Prossimo](https://www.memorysafety.org/docs/memory-safety/#fn:1)


#### OS reads
- [Processes](https://web.eecs.utk.e inspiring in the face of challdu/~smarz1/courses/cosc361/notes/processes/)
- [Linker Scripting](http://bravegnu.org/gnu-eprog/lds.html)

#### Wasmtime
- [Wasmtime official Docs](https://docs.wasmtime.dev/introduction.html)
- [Wasmtime crate Docs](https://docs.rs/wasmtime/latest/wasmtime/)

#### Rust Error Handling
- Anyhow

#### ELF Files
- [elf file format specifications](https://osblog.stephenmarz.com/files/elf.pdf)
- The ELF file Specifications : (from [Linux Foundation](https://refspecs.linuxfoundation.org/elf/elf.pdf)) (From [CMU.edu](https://www.cs.cmu.edu/afs/cs/academic/class/15213-f00/docs/elf.pdf))
- https://wiki.osdev.org/ELF
- [Tools in inspecting Elf files](https://linuxhint.com/understanding_elf_file_format/)

#### Booting
- [Booting in x86 CPU](https://os.phil-opp.com/minimal-rust-kernel/#the-boot-process)
- [Booting In RISCV](https://osblog.stephenmarz.com/ch1.html)

#### Miscelleneous
- [multitasking](https://os.phil-opp.com/async-await/)
- [Patterns..including the singleton pattern](https://refactoring.guru/design-patterns)
- [OS beginner mistakes](https://wiki.osdev.org/Beginner_Mistakes#Is_there_a_tutorial_on....3F)... and debunking them
- [How to conduct an Buffer_overflow attack](https://samsclass.info/127/proj/p3-lbuf1.htm)

#### UART
- [The UART Datasheet](http://caro.su/msx/ocm_de1/16550.pdf)
- [The UART specifications](https://www.lammertbies.nl/comm/info/serial-uart)
- [Stephen's tutorial](https://osblog.stephenmarz.com/ch2.html)


curiosity over fear  
innovation over practicality'     
authenticity over professionalism   
Learning over Grades  
