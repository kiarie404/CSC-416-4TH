# Filesystem

## Theory
**What is a file?**  
A file is a named collection of static data.  
Data can be a byte, a couple of bytes or gigabytes.  
The data collection needs to be static so as to store data persistently. So in this case, files can be redefined as : A named data collection in a persistent storage device.  

***File Types***  
There are different types of files. Types emmerge due to either "the file format" or "the file purpose".  
For example an elf file has a different format to a .exe file... but they serve the same purpose.  
A data file has the same format as a system file... but they erver different purposes

File Types include :
1. Regular Data Files : These are files that contain user-level information. It can contain Binary data or ASCII data.
2. Directory Files : These are files that describe the structure of the file system

***Block device***  
A block is a kind of storage device that allows data to be read and written to it in fixed sizes called blocks. A block is a large group of bytes. From this, it it clear that a block storage device consisting of many many bytes. For example a HDD device or SSD device.

***A Character device***  
A character device is also a storage device that only allows data to be read and written to it character-wise... or rather byte-wise.

Block devices are suitable in situations where we need performance. This is because Character devices force the CPU to do more I/O operations. I/O operations are expensive as hell.  We always need performance

Character devices are suitable when we need data in a stream-wise fashion... a continuous fashion. Eg data from a keyboard or a mouse. Keyboards and Mice are character devices.

***A disk Partition***
A disk is a fancy name for a hardisk ... a hardisk is a type of a block device.   
A partition scheme is a specification of how to logically seperate bytes in a hard disk into seperate sections called partitions.  

We have many partitioning specifications eg MBR, UEFI, GPT LVM  
If you use a partitioning software to partition your disk, your disk will typically have 3 parts...
1. The partial_boot code : The partial boot code will help the firmware locate the bootloader that may be found in one of the partitions
2. The partition table 
3. The actual Partitions

The Partition table contains metadata about the partitions. It typically contains info such as :
- The file systems found in each partition
- The status flags of each partition eg : Bootable flag, writable flag
- The size of each partition
- The memory boundaries of each partition
- The type of each partition (primary? swap? extended? logical? )

***A File system***  
A file system is a software definition of how the bytes in a block device are organized. To be more specific a file system is a software definition of how the bytes in a **partition** are organized.   
We have many file systems in existence...each with their own usecase and trade-offs. For example : NTFS, ExFAT, EXT4, BTRFS and Minix 3 File system.  

We will focus on the Minix 3 file system... but with tweaks

### Minix 3 File System (our Variation)

The minix 3 Filesystem organizes the bytes within a partition as follows :  
![Minix 3 file system](../images/Minix%203%20filesystem.png)

**A block** is a group of bytes. Like in minix 3, a block typically means 1024 contiguous bytes. But this is not always the case. For example, the superblock is only 32 contiguous bytes.

**A zone** is a group of blocks. Like in Minix 3, a zone usually contains 4 contiguous blocks.

**A bitmap** is an array of bits.

#### **The Boot block** 
This section might contain boot code for an operating system. If there is no operating system...this section is filled with zeros. The bootblock is 1024 bytes long. Sometimes the 1024 byte is not enough... in such a case, part of the boot code will get stored in the Data zone.

#### **The SuperBlock**  
The Superblock is 32 bytes long.  
The Superblock contains metadata about the entire Filesystem. Here is a struct showing the data contained in a superblock :  
```rust
#[repr(C)]
pub struct SuperBlock {
  pub ninodes:         u32,  // the total number of index nodes
  pub pad0:            u16,
  pub imap_blocks:     u16,  // 
  pub zmap_blocks:     u16,
  pub first_data_zone: u16,
  pub log_zone_size:   u16,
  pub pad1:            u16,
  pub max_size:        u32,
  pub zones:           u32,
  pub magic:           u16,  // identifies the file system type (Minix == 0x4d5a)
  pub pad2:            u16,
  pub block_size:      u16,
  pub disk_version:    u8,
}
```

The superblock does not change once the partition gets mounted. This is because the superblock contains static information such as "the number of inodes" which will only change when you resize the disk.  

Our file system code only needs to read from this Superblock structure. Recall that we can find this structure after the boot block. The default block size of the Minix 3 file system is 1,024 bytes, which is why we can ask the block driver to get us the super block using the following.



```rust
// descriptor, buffer, size, offset
// synchronous_read ... it's synchronous because we suspend the calling code until the read operation is done.
// If it were an asynchronous read... the calling code would have continued doing other things as it waits for the I/O operation to end.
syc_read(desc, buffer.get_mut(), 512, 1024);
```

The super block itself is only about 32 bytes; however, recall that the block driver must receive requests in sectors which is a group of 512 bytes. Yes, we waste quite a bit of memory reading the super block, but due to I/O constraints, we have to.  This is why I use a buffer in most of my code. We can point the structure to just the top portion of the memory. If the fields are aligned correctly, we can read the superblock by simply referencing the Rust structure. This is why you see #[repr(C)] to change Rust's structure to a C-style structure. 

The Superblock is 32 bytes but our block read function reads 512 bytes at once. So we will just read the 512 bytes and store them in a buffer... from there we can just disect it using offsets of u8


#### The Padding to even out a block to 1024 bytes

Considering the superblock

#### THe Z map and imap
The Z map is a bunch of 1-bit arrays. Each bit represents an existing zone. 1 means the zone is taken and 0 means the zone is free.  
The Imap is a bunch of 1-bit arrays. Each bit represents an existing inode. 1 means the zone is taken and 0 means the zone is free.  





## Implementation

If you make a function a process, it can be executed independently if it is pure non_dependent code.  
Our file block driver initially did synchronous reads. Synchronous reads bad for performance.  
We need to mak our file reads asynchronous. Asynchronous reads are good for performance.  
We can make our file reading asynchronous by turning the file reading operation into a kernel process... instead of a procedural function. This kernel process will only be ready to get executed if we receive an interrupt from the block device.(the block device sends interrupts to signal that it has finished a read operation).  







CAN I JUST USE THE PREDEFINED MINIX FILESYSteM?????!!!!