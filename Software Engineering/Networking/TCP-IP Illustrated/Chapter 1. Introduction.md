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
