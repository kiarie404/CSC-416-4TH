# The RAM Management

### How much RAM do we have?
Qemu is a virtual environment... meaning we get to define how much RAM our machine has. We are humble and efficient... so we choose 128 MBs only... right?  

The RAM that we are dealing with is only 128 MBs as specified in our Qemu configuration : You can see a " -m 128M" setting in the build and run Config file :  
```toml
[target.riscv64gc-unknown-none-elf]
runner = "qemu-system-riscv64 -machine virt -cpu rv64 -d guest_errors,unimp -smp 4 -m 128M -drive if=none,format=raw,file=hdd.dsk,id=foo -device virtio-blk-device,scsi=off,drive=foo -serial mon:stdio -bios none -device virtio-rng-device -device virtio-gpu-device -device virtio-net-device -device virtio-tablet-device -device virtio-keyboard-device  -kernel "
```

If you want to add more, go ahead... so wasteful.


### How will we manage the RAM ?

To manage the RAM we need to do the following tasks well :  
1. Safely Abstracting the physical memory bytes using software.
2. Define methods of correctly allocating memory.
3. Define methods of correctly deallocating memory.
4. Defining methods of accessing the correct memory sections
5. Provide a clean API

