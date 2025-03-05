# Chapter 1. Introduction

## System Calls and Library Functions
* All operating systems provide service points through which programs request services from the kernel
* These kernel entry points are known as _system calls_

## Network Implementation Overview
* Kernel networking code is organized in three layers (from highest-level to lowest-level: socket, protocol, interface)
* Socket layer: protocol-independent interface to the protocol-dependent layer below. System calls start at the protocol-independent socket layer
* Protocol layer contains implementation of the 4.4BSD communication protocol familes (for example, TCP/IP - the Internet protocol suite, Unix domain protocols - form of interprocess communication)
* Protocol layer abstracts internal structure. For example, Internet protocol suite has the network layer as its lowest layer and the two transport layers (TCP/UDP) above it

## Descriptors
* Example code calls `socket` specifying the type of socket by passing the Internet protocol family value and datagram socket value. This combination generates a socket with the UDP protocol.
* `socket` returns a descriptor that shares properties of other Unix descriptors. Can `read`, `write`, etc.

### Data Structures
* Process table entry for the process calling `socket`
* Entry exists for each process during the process’ lifetime
* Descriptor is an index in an array within the process table entry
* The descriptor’s array entry points to an open file table structure
* This open file table structure points to an i-node or v-node structure describing the file
* When executing a system call, the kernel has access to the process table structure
* `p_fd` within the process table points to the `filedesc` structure for the process
* Within `filedesc`, there are pointers to per-descriptor flags and points to the file table structure, containing `file` structures
* These `file` structures contain information about the type of descriptor
* v-nodes are are a file type that allows the kernel to support different types of file systems
* TCP/IP sockets have an `f_type` value of `DTYPE_SOCKET` (vs. `DTYPE_VNODE`)
* The `f_data` member of the `file` structure points to a `socket` or `vnode` structure
* The `f_ops` member of the `file` structure points to functions that are used by the `read`, `write`, `close`, etc system calls as these system calls work with a socket or non-socket descriptor
* The `socket` structure referenced by the socket file descriptor references an Internet protocol process control block
* The Internet protocol process control block has information like the origin IP address and port as well as the destination address and port
* System call flow: kernel starts with descriptor value -> `fd_ofiles` -> descriptor `file` structure -> `socket` structure -> Internet protocol control block
* UDP datagram arrives on network interface flow: kernel searches through all the UDP process control blocks, finding the appropriate one based on the destination UDP prot number and destination IP address, source IP address, source port numbers -> `socket` structure

## Mbufs (Memory Buffers) and Output Processing
* Socket layer verifies references in system calls, like the pointer that points to a piece of memory in the address space of the process
  * After verification, copies socket address structure into an mbuf
* mbufs are linked together using `m_next` and `m_nextpkt` members
* `m_pkthdr.len` and `m_pkthdr.rcvif` are members of the mbuf header
  * `len` member contains the total length of the mbuf chain to avoid having to go through all the mbufs on the chain to calculate the total length
* mbufs are always 128 bytes
* First mbuf provides 100 bytes of data storage on the first mbuf on the chain
  * Subsequent mbufs provide 108 bytes of storage
* When the necessary storage data exceeds 208 bytes, a larger buffer called a cluster is used instead of mbufs
* After the socket layer copies the destination socket address structure into an mbuf, the protocol layer corresponding to the socket descriptor is executed
* For a UDP socket, the UDP output routine is called
  * Pointers to the mbufs are passed as arguments
  * UDP output routine prepends an IP header and a UDP header in front of the existing mbuf content
  * A new mbuf is allocated, made the head of the chain
    * Packet header information (`m_pkthdr.len` and `m_pkthdr.rcvif`) is copied from the previous head of the chain
    * IP and UDP headers are stored at the end of the new mbuf that becomes the head of the mbuf chain
    * Any lower-level protocols (interface layer) can prepend their headers in front of the IP header, if necessary, without having to copy the header data
* UDP output routine fills in the UDP header and as much of the IP header possible
  * The destination address in the IP header can be set, but the IP checksum is left to the IP output routine to calculate and store
* UDP checksum is calculated and stored in the UDP header
* Complete pass of the original bytes of data stored in the mbuf chain
  * So far, kernel has made two passes of the original mbuf data
  * Once to copy data from the user's buffer into the kernel's mbufs
  * Another time, when calculating the UDP checksum
* UDP output routine ends up calling the IP output routine, passing pointer to the mbuf chain
* IP output routine fills in data like the IP checksum, determines the outgoing interface for the outgoing datagram (the IP routing function), calls the interface output function
* Ethernet output routine prepends the Ethernet header to first mbuf in chain
* mbuf chain is added to end of the output queue for the defined interface
* When the interface processes an mbuf on its output queue, it copies the data to its transmit buffer and initiates the output
  * Example contains the following bytes copied to transmit buffer:
    * 14-byte Ethernet header
    * 20-byte IP header
    * 8-byte UDP header
    * 150 bytes of user data
  * Third complete pass of the data by the kernel
  * After copy, the mbuf chain is released by the Ethernet device driver
  * After release, mbufs become part of the kernel's pool of free mbufs

## Interrupt Levels and Concurrency
* In basic example, socket layer is operating at `sp10` (normal operating mode, nothing blocked, lowest priority)
* Ethernet device driver interrupt occurs
* Interface layer now executes at a higher priority (`splimp`) indicating network device I/O
* This interrupt preempts the socket layer code
* While the Ethernet device driver is running, it places a received packet on the IP input queue and schedules a software interrupt
  * This interrupt has a priority of `splnet` or network protocol processing
  * This interrupt won't take effect immediately because the kernel is running at a higher priority level (network device I/O)
* When the Ethernet device driver completes, the previously scheduled protocol layer begins execution
* A terminal device interrupt occurs and it is handled immediately, preempting the protocol layer since terminal I/O has higher priority than the protocol layer
* Let's say the terminal device interrupt involves putting a packet onto the IP input queue and scheduling another software interrupt for the protocol layer
* Preempted protocol layer continues
  * Finishes processing the packet received from the Ethernet device driver
  * Then processes packet received from terminal device
  * Only when there are no more input packets to process will control be returned to whatever was preempted (the initial socket layer operation)
