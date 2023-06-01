
- Cover
    - Uon Stuff
    - Project title

- Declaration and signatures
- Acknowledgements
- Abstract (summary)

- Introduction
  - background
  - Problem Definition
    - Human Problem
    - Technical Problem
  - Solution
  - Objectives
    - System Objectives
    - Research Objectives
  - Scope
  - Project Justification
  - Assumptions

- Literature Review
  - outlook of the IoT development architecture
  - Rust
  - Wasm
  - Riscv
  - OS designs
  - wasm Runtimes
  - Docker
  - problems of the IoT architecture
  - Solution proposed
  - Related Solutions
    - kernel bit
    - Docker solution

- System Methodology
  - project planning 
  - Research methodology
  - system development methodology
    - testing
    - documentation
    - design

- System analysis
  - functional requirements
  - system requiremets
  - resources/ tools used
  - Time
  - Feasability study
  - constraints
  
- System Design
  - overall designs
  - design of each module
  - Explain the tradeoff in each design choices


- System Implementation and testing
  - define the tools used through out the process (why, how and where used)
  - provide snippet links to working code
  - Define testing framework

- challenges
- Conclusion


- References
  - arrange by topics
- Appendices
  - installation of Tools
  - Installation of program



Abbreviations
ISA - Instruction Set architecture.
IOT - Internet of Things
API - Application Programming Interface

References 
[1] - Taivalsaari, On the development of IoT systems



building an average IoT system requires the development team to use a lot of unassociated tech. For example; Assembly for the hardware, C/Rust for embedded programming, distributed programming for building the immediate network infrastructure, docker and kurbenetes for implementing microservices over the cloud, web languages to build a website that acts as a remote interface to the embedded devices... And probably kotlin for a mobile app that interacts with the embedded devices. 
There is a high chance each module will be handled by different teams, each 
