---
layout: post
title:  "Hardware / Virtualization notes"
date:   2019-07-12 14:18:25 +0800
categories: infosec
---
> Hardware / Virtualization notes

# Hardware

Here, I will be posting all my notes regarding hardware and virtualization.

### Motherboard
- Different sizes: E-atx (extended), atx, micro-atx, mini-itx
- Sockets: the connector the CPU processor has. It has to match with the motherboard socket.
- Buses: Moves data between components that connect the motherboard.

### Processors
- Cores: Responsible for executing instructions
- Clock speed: Instructions executed per second (1 Hz - 1 instruction per second, 1 GHz - 1 billion instructions per second)
- Context switching: Processor sharing processor time between applications.
- Heat sink: thermally conductive material, drags heat away. Thermal paste conducts heat and traps air (good insulator)

### Register
- EAX (Extended Accumulator Register) 32 bit - General purpose
  - AX 16 bit
  - AH/AL 8 bit
- EIP (Extended Instruction Pointer) - Holds address of next instruction
- Others include EBX, ECX, EDX (can be split up)
- Here are the class of unseparable registers:
  - ESP is the stack pointer register (memory adress pointing to the top of the current frame in the RAM)
  - EBP is the base pointer (points to the bottom of the current RAM)
  - ESI is the source index (holds memory address of data when the data is being used as a source in the operation i.e. copying data)
  - EDI is the destination memory address (holds the destination memory address i.e. where the memory is being copied to)
    
### Fetch-decode-execute
- Control unit fetches next instruction from RAM and decodes it
- ALU executes the instruction
- 4 instructions in total

### RAM
- Think of it as the number of memory available to run programs
- Faster speed (read data from ram), Volatile
- DDR4 (double data rate fourth generation) - newest

### Storage
- Desktops use 3.5 inch whilst laptops uses 2.5 inch form-factors
- Mechanical: Slower than ssd, has moving parts and wears out
- SSD: Faster, no moving parts, more reliable

### GPU
- Dedicated processor performing calculations for graphics, also has dedicated RAM in it
- Also used for encryption, cracking numbers, 3d rendering

### PSU
- Drives power from power to components such as motherboard, graphics card and hard drives
- 4 pin peripheral connector powers fans, SATA powers hard drives, 20/24 pin connects motherboard, PCI-E powers graphic cards

### IO devices

##### Input
- USB 
  - USB 1 - Transfer Speed: 1.5 Mbit/s
  - USB 2 - Transfer Speed: 480 Mbit/s
  - USB 3 - Transfer Speed: 4 Gbit/s (You can usually tell if a port is USB 3 compatible because the port will be coloured  blue)
  - USB 3.1 - Transfer Speed: 10 Gbit/s (You can usually tell if a port is USB 3.1 compatible because the port will be coloured turquoise)
- PS/2 ports for older peripherals

##### Output
- VGA: the oldest standard for A/V output
- DVI: a more modern version of VGI
- HDMI: more modern, can also transmit audio
- Display port / usb-c: newest standard

## File Systems
- It's like a protocol for accessing files on and saving files to storage
- ***Clusters***
  - Every storage device is broken down into a series of clusters (the smallest section of the disk that can be used to store a file)
  - *Slack space* is saving a 1kb file on a 32kb cluster, wasting 31kb
  - Each cluster has an address
- Usually, a metadata (used to index and store info for the file) and a content file is stored per file -> Why we can recover files: we simply delete the metadata from the index and mark the content cluster as overwritable

## FAT32
- FAT32 is introduced with Windows 95. Uses File Allocation Table to map each cluster
- Doesn't support files larger than 4GB
- Doesn't support file permissions because it doesn't store metadata for who created a file
- Used primarily in USB drives 

## exFAT
- Designed for USBs as well, doesn't support file permissions
- Larger file size, designed to replace FAT32

## NTFS
- New Technology File System, used by modern windows
- Includes permissions support, encryption support and shadow copies (backup files effectively)
- Large file size limit
- Can heal from data corruption
- Limited support by non-Windows system

## EXT3
- Extended File System is used in linux
- Supports permissions and encryption but not shadow copy
- Max file size of 2TB
- 'Journaling' file system, means that changes to the disk are tracked in a separate file system called the journal
  - Can recover the drive due to corruption

## EXT4
- Modern version of EXT3
- Supports permissions, encryptions, no shadow copy
- You can turn off journal, has faster disk check process

## HFS+
- Hierarchal File System Plus is a proprietary file system only compatible with Mac OS
- No file size limitations, has a journal system with permissions and encryptions

## APFS
- Apple File System is the modern Mac file system
- Duplicate files can be stored without additional space, with changes to one copy of a file being saved as a delta (diff old & new)

# Misc

## File headers

- Commonly termed magic bytes / file signatures.
- Tells us the type of file at the beginning of a file
- A plain text file doesn't have a file header 
- 50 4B 03 04 (for a normal zip archive)
- 50 4B 05 06 (for an empty zip archive)
- 50 4B 07 08 (for a spanned zip archive)
- jpg files FF D8 FF DB or FF D8 FF E0 or FF D8 FF E1
- Uses pairs because 2 hex characters = 1 byte (8 bits) of data58 68 63 31 4a 69 36 43 64 7a