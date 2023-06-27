# wasm_from_book

- [ ] why the stack-machine chosen for wasm? (portability, efficiency, binary_size)
- [ ] What is type safety? Why is typesafety important? How does wasm ensure typesafety?
- [ ] How does wasm implement bounds checking
  - 

- [ ] How does webassembly handle control flow attacks?
- [ ] What are the possible control instructions in wasm?

- [ ] What is linear memory in wasm?
- [ ] Why do we need linear memory? Why is it important?
- [ ] What are the trade-offs brought about by using linear memory? What challenges does it bring? WHat efficiencies does it bring?
- [ ] What operations can be done on the memory? Internally and externally.

- [ ] How do you add more linear memory? By how much?
- [ ] How do you delete memory?
- [ ] How does linear meory contribute to security?

- [ ] Why do we need to inspect and debug wasm files?
- [ ] Which tools do we need to inspect and debug wasm files?
- [ ] How can we use these tools for security inspection of wasm modules?
- [ ] Difference between wasm-objdump --disassemble and 

- [ ] Why would anyone convert wasm to C/Rust?
- [ ] How do you do that conversion? Is it viable? Can it be used across teams that use different High-level languages?

- [ ] Now that wasm code can be disassembled, inspected and  to high-level code... how will one be able to deploy patented code? How will secretive code be deployed? Many kinds of object code can be disassembled, how did they achieve secrecy in deployment?
- [ ] 

* the host and wasm_mod can communicate via the linear memory


## Answers 
- [ ] How does wasm implement bounds checking
  - the programmer can set boundary checks using the (unreachable) instruction. This causes a runtime error
  - 