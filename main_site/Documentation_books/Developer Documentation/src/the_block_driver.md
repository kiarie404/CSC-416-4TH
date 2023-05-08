# The Block Driver

Theory : Virtio Protocol



VirtIO protocol is a communication protocol. It defines how a virtual machine communicates with a hypervisor.  
Okay... now you are asking "what do virtual machines and hypervisors have to do with us? we were just here chilling and talking about bare-metal operating systems!"

The thing is that Virtio protocol defines Virtio devices. Virtio devices are virtual devices that have a simple standard interface. For example, it defines network cards and hard disks. Qemu uses provides us with this devices.  
In our case, we will act ad the VM  and Qemu will act as the Hypervisor.

We will deal with virtio block devices. We will write a driver for a Virtio Block device.

![Virtio memory layout part 1](../images/Virtio%20MMIO%20Register%20Layout%20part%201.png)
![Virtio memory layout part 2](../images/Virtio%20MMIO%20register%20layout%20part%202.png)
![Virtio device type IDs](../images/virtio%20device%20IDs.png)

For the QEMU emulator, it puts virtio devices (backwards) from 0x1000_1000 to 0x1000_8000. If we only have one device, it should be attached at 0x1000_8000  
Qemu supports 8 Virtio buses. Each having 4096 byte register space.

We use MMIO programming to communicate with the underlying Virtio device
MMIO registers for Virtio in Riscv are 32 bits only

***Abstracting the MMIO Bus address full of registers***  

```rust
// Below are offsets
#[repr(usize)]
pub enum MmioOffsets {
  MagicValue = 0x000,
  // Version = 0x004,
  DeviceId = 0x008,
  VendorId = 0x00c,
  HostFeatures = 0x010,
  HostFeaturesSel = 0x014,
  GuestFeatures = 0x020,
  GuestFeaturesSel = 0x024,
  GuestPageSize = 0x028,
  QueueSel = 0x030,
  QueueNumMax = 0x034,
  QueueNum = 0x038,
  QueueAlign = 0x03c,
  QueuePfn = 0x040,
  QueueNotify = 0x050,
  InterruptStatus = 0x060,
  InterruptAck = 0x064,
  Status = 0x070,
  Config = 0x100,
}


```

- *probe function* - We first need to scan the buses dedicated to connecting the virtio devices (from 0x1000_1000 to 0x1000_8000) and check which devices is attached to each bus 
  - loop through the buses in a bus-wise fashion
  - confirm that indeed each bus is a virtio bus address by reading the magic value
  - check if a device is connected to the bus. (if device_id > 0)
  - Check the type of device attached to the bus (device ID 2 == block device)
  - If we find that the bus is a virtio bus and device id 2, we can configure this device as a block device. 

***configuring the device (initializing_the_device)***  
Before we use the device(any device in the world), we must set the right configurations by adjusting the values in the MMIO registers.  
These steps are commonly referred to as the "initialization handshake". They are used to establish communication between the driver and the device.

The steps for configurating a device are laid out in the virtio specification as ([source](https://docs.oasis-open.org/virtio/virtio/v1.1/cs01/virtio-v1.1-cs01.html#x1-920001)):

pre-requisites : 
- [**Device Status Register** fields](https://docs.oasis-open.org/virtio/virtio/v1.1/cs01/virtio-v1.1-cs01.html#x1-100001) 
- 

1. Reset the device by writing 0 to the Device status register. Resetting means setting values to known default values. This removes any uncertainities or unpredictable behaviour, we begin our communication on a clean slate. No surprises.
  - Reading from the Device status register returns the current device status flags.  
  - Writing non-zero values to this register sets the status flags, indicating the OS/driver progress. 
  - Writing zero (0x0) to this register triggers a device reset. The device sets QueuePFN to zero (0x0) for all queues in the device.


2. Set the ACKNOWLEDGE status bit to the status register.
  - within the device status register, there is a single bit called the Acknowledge status bit.
  - The Acknowledge status bit value is 1 when both the driver and device agree that they are connected to each other and can continue with initialization. This bit answers the question "Are we connected to each other? And are all of us okay with this connection?"
  - The driver sets the Bit to 1 to acknowledge that it has access to the device registers and has found it to be a valid virtio device(connected).
  - The device reads, the bit and also resets it to 1 to confirm that it is okay with the connection.  
  - The device cannot reset the bit to 0. If an error occurs within the device and the device is not okay with the connection, it sends a message through other status bits such as DEVICE_NEEDS_RESET or DEVICE_FAILED bits, to indicate that the initialization process has failed and needs to be restarted in order to create a proper connection.  


3. Set the DRIVER status bit to the status register 
  - The driver status bit indicates that the Guest has a driver that can drive the device.  
  - After the driver sets the Acknowledge bit to 1, it sets the DRIVER status bit to 1 to indicate that it is in control of the device and is ready to negotiate the features that the device supports. The device then acknowledges this by setting the ACKNOWLEDGE status bit to 1 and waits for the driver to read the device features from the host_features register.


4. Read device features from **host_features register**.
  - Here are the [Features of the virtio block device](https://docs.oasis-open.org/virtio/virtio/v1.1/cs01/virtio-v1.1-cs01.html#x1-2420003)
  - Each virtio device offers all the features it understands. During device initialization, the driver reads this and tells the device the subset that it accepts. The only way to renegotiate is to reset the device.  
  - This allows for forwards and backwards compatibility: if the device is enhanced with a new feature bit, older drivers will not write that feature bit back to the device. Similarly, if a driver is enhanced with a feature that the device doesn’t support, it see the new feature is not offered. 
  - Feature bits are allocated as follows:
     0 to 23
         Feature bits for the specific device type 
     24 to 37
         Feature bits reserved for extensions to the queue and feature negotiation mechanisms 
     38 and above
         Feature bits reserved for future extensions.
   Note: For example, feature bit 0 for a network device (i.e. Device ID 1) indicates that the device supports checksumming of packets.


5. Negotiate the set of features and write what you'll accept to **guest_features register**.
  - The Guest_features register contains Write_only Flags representing device features understood and activated by the driver.
  - Fill this out so as to establish compatibility between  

6. Set the FEATURES_OK status bit to the status register '
   - The FEATURES_OK status bit Indicates that the driver has acknowledged all the features it understands, and feature negotiation is complete. 
   - The driver sets the FEATURES_OK status bit to indicate to the Virtio device that it has negotiated a set of compatible features. 
   - During device configuration, after the driver negotiates the set of features and writes what it will accept to the guest_features register, the device checks if it can support the negotiated features. If the device does not support all the features negotiated by the driver, it may set the FEATURES_OK status bit to 0 to indicate that it cannot accept the negotiated features
 
7. Re-read the status register to confirm that the device accepted your features. The driver might have reset the Features_OK register to 0.


8. Perform device-specific setup - The driver performs any device-specific setup required to initialize the Virtio device, such as configuring queues and memory mappings.
   - Set **Queue Number register**.
     - The QueueNum is a register in the device's configuration space that specifies the maximum number of virtqueues supported by the device. By default, the Queue Number is set to 1...such that there is only one virtual queue occupying all of the buffer space.
     - But you may need multiple queues to implement parallelism. So you will have multiple queues under the same buffer space.
     - The **Queue_Num_Max register** is provided by the device (Read only), it specifies the total amount of parallel queues you can have 
   - VIRTIO_RING_SIZE is a constant defined in the VirtIO specification and represents the maximum number of elements (or "entries") in a VirtIO ring (default == 1024). A VirtIO ring is a data structure used by the VirtIO device and the driver to communicate with each other. It is a circular buffer of fixed size that is divided into two sections: the "available ring" and the "used ring". 


  Actual :
    - step 7 :
      - read Queue_Num_Max register and see the max number of queues that is supported for parallelism
      - We randomly chose 1024 (VIRTIO_RING_SIZE) to be the number of parallel queues
      - We calculate the number of pages required to store the Queue structure for the block device. We add PAGE_SIZE - 1 to the size of the Queue structure (which is obtained using the size_of function), then divides the result by PAGE_SIZE to round up to the nearest page. The addition of PAGE_SIZE - 1 is to ensure that the calculation rounds up to the nearest page, so that there is no wasted memory.
      - We create space for the virtual queue in our driver. We do this by allocating pages that will be used by the queue
      - allocate memory for the queue and stores the physical address of the queue in the queue_pfn 

1.  Set the DRIVER_OK status bit to the status register. The device is now LIVE.
    - The DRIVER_OK status bit indicates that the driver is set up and ready to drive the device. 
    - The driver sets the DRIVER_OK status bit to 1 to indicate to the Virtio device that initialization is complete and it is ready to use the device.


***Communicating***
Now that the device is LIVE, we can start making requests by using the virtio rings.  
The virtio descriptor/ring system is generic; however, we have a protocol when making block requests. We will make a block request using three descriptors: 
  (1) block request header  
  (2) block request buffer  
  (3) block request status.  

The Block request header specifies the type of operation and the sector number  
After the header, we store the data buffer. For reads, the device will write to this piece of memory, and for writes, the device will read from this piece of memory. It's important to note that these must be physical addresses, since the block device bypasses the MMU.  

Finally, we have a status field. The device will write the result of the request to this 8-bit field. There are currently only three responses we can get: 0-success, 1-failure, 2-unsupported operation. That doesn't give us a lot of information, but if we get a 0, we can reasonably assume that our request was properly handled.  

When making a request to the device, the memory we use to store the request must remain valid until the device has finished processing the request and has sent back a response. If we use memory that is allocated on the stack, it will be automatically deallocated when the function exits, which could lead to undefined behavior if the device tries to access that memory later.  

To avoid this issue, we need to allocate heap memory to store the request. This memory will stay resident until we explicitly deallocate it, so we can be sure that it will remain valid until we have received a response from the device.

We will grab three open descriptors from the virtio queue, populate it with the header, buffer, and status, and then we write the virtqueue's number (0) into the queue_notify register to tell the device to start working on the request. 




I will import the block driver... this bock driver has too much detail




-------  
- "step by" in Rust
- volatiles in Rust
