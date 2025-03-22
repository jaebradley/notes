# Chapter 2. Mbufs: Memory Buffers

* Packet header `mbuf`
  * Has `m_flags` value of `M_PKTHDR`
  * First `mbuf` describing a packet of data
  * Data is still contained within the mbuf, but because 8 bytes are taken up by the packet header, only 100 bytes of data fit into the `mbuf`
* If an `mbuf` contains more than 208 bytes of data, it references an external buffer called a cluster
  * `mbuf` still contains space for the packet header structure, but it's unused
  * Instead of using multiple `mbufs` to contain the data, Net/3 allocates a cluster of 1KB or 2KB in size
    * The `m_data` value in the mbuf points somehwere inside the cluster
    * Older systems used 1KB clusters to save memory
    * Newer systems use 2KB clusters as memory has gotten cheaper to increase performance
* `mbufs` are always 128 bytes
* `m_data` pointer can point anywhere in the corresponding buffer, doesn't necessarily have to be at the beginning of the buffer
* `mbufs` with a cluster always contain the starting address of the buffer
* `m_next` pointer links together the `mbufs` forming a single packet into an `mbuf` chain
* `m_nextpkt` pointer links multiple packets together to form an `mbuf` queue
  * Each packet on the queue can be a single `mbuf` or an `mbuf` chain
  * First mbuf of each packet contains a packet header
    * This is the only mbuf in an mbuf chain that references the next packet

## 2.6 `m_devget` and `m_pullup` Functions
* `m_pullup` function is called to guarantee that the specified number of bytes are contiguous in the first `mbuf` of a chain
  * The specified number of bytes are copied to a new mbuf and made contiguous

### `m_devget` Function
* When an Ethernet frame is received, the device driver calls the `m_devget` function to create an `mbuf` chain
  * Copies frame from device into the chain
* When the amount of data is between 0 and 84 bytes
  * Example is 52 bytes of data
    * 20 byte IP header
    * 32 byte TCP header
    * No TCP data
  * Minimum size for the `mbuf` is 28 bytes
    * 20 bytes for the IP header
    * 8 bytes for a UDP header
    * 0 bytes for a zero-length UDP datagram
  * 16 bytes are left unused at the beginning of the `mbuf`
    * Room is allocated for a 14 byte Ethernet header on output, if the same `mbuf` is used for output
* If the amount of data is between 85 and 100 bytes
  * Data still fits in a packet header `mbuf`
  * No room for 16 bytes at the beginning of the `mbuf`
  * Data starts at the beginning of the `m_pkdat` array and any unused space is at the end of the array
* Two `mbuf`s are required when the amount of data is between 101 and 207 bytes
  * First 100 bytes are stored in the first (packet header) `mbuf` and the remainder are stored in the second `mbuf`
* If the amount of data is greater than or equal to 208 bytes, one or more clusters are used
  * Each cluster would be referenced by a different `mbuf`

### `m_pullup` Function and Contiguous Protocol Headers
* `m_pullup` is called when a protocol (IP, ICMP, IGMP, UDP, TCP) finds that the amount of data in the first `mbuf` is less than the size of the minimum protocol header
  * 20 for IP, 8 for UDP, 20 for TCP
  * `m_pullup` is called on the assumption that the remaining part of the header is in the next `mbuf` on the chain

### `m_pullup` and IP Fragmentation and Reassembly
* IP fragmentation algorithm keeps individual fragments on a doubly-linked list
* The source and destination IP address fields in the IP header are used as forward and backward list pointers
* A received packet has a length of 296 bytes and is a fragment of a larger IP datagram
  * IP header is in a cluster that is referenced by the packet header `mbuf`
* IP fragmentation algorithm keeps the individual fragments on a doubly linked list
  * Uses the source and destination IP addresses in the IP header to hold forward and backward list pointers
* IP fragmentation routine always calls `m_pullup` when fragment is received and the fragment references a cluster
  * This logic forces the 20-byte IP header into its own `mbuf`
  * The cluster reference from the original `mbuf` is changed to the new starting point (after the IP header-related data has been moved to its own `mbuf`)
  * New packet header `mbuf` with the IP header information references the original `mbuf` (that references the cluster with the remaining packet data)




