# Integration Tests

These test do not focus on only one specific module. They Focus on testing how multiple modules work together.  
The tests are seperate from the source crate. This is because they are testing the crate as an external entity. They only interact with the public API of the crate.    

More explanations on unit tests can be found [through this link](https://doc.rust-lang.org/book/ch11-03-test-organization.html#integration-tests)    



These repositories are found in the : [undone]

Here are the itegration tests done:
- [ ] Test if stdout and stdin work (while being interrupt-driven)
- [ ] Test if Memory Initialization and allocation works
  - [ ] page allocation
  - [ ] Byte allocation
- [ ] Test if Paging works well
- [ ] Test if Kernel Memory Mapping works well
- [ ] Test if Exception Handling works well while in kernel is running in Supervisor mode
- [ ] Test if Interrupt Handling works well while in kernel is running in Supervisor mode


