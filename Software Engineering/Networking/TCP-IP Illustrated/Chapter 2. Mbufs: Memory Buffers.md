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
* A received packet has a length of 296 bytes and is a fragment of a larger IP datagram
  * IP header is in a cluster that is referenced by the packet header `mbuf`
* IP fragmentation algorithm keeps the individual fragments on a doubly linked list
  * Uses the source and destination IP addresses in the IP header to hold forward and backward list pointers
* IP fragmentation routine always calls `m_pullup` when fragment is received and the fragment references a cluster
  * This logic forces the 20-byte IP header into its own `mbuf`
  * The cluster reference from the original `mbuf` is changed to the new starting point (after the IP header-related data has been moved to its own `mbuf`)
  * New packet header `mbuf` with the IP header information references the original `mbuf` (that references the cluster with the remaining packet data)

### Avoidance of `m_pullup` by TCP Reassembly
* `m_pullup` is expensive as memory is allocated and data is copied from cluster to an `mbuf`
* About 50% of TCP data is "bulk data" - 512+ bytes per segment
* About 90% of the other 50% of TCP data is itneractive data (< 10 bytes of data)
* When TCP segments arrive out of order, they are stored on a doubly-linked list by TCP
* Problem similar to when list pointer is stored in the IP header, which is stored in the cluster
* TCP stores an `mbuf` pointer in some unused fields in the TCP header providing a back pointer from the cluster to the `mbuf` to avoid calling `m_pullup` for every out-of-order segment

## `m_copy` and Cluster Reference Counts
* Clusters reduce the number of `mbufs` required to contain large amounts of data
* Would need 10 `mbufs` to contain 1024 bytes of data
* Clusters have the potential for wasted space
  * Takes 2176 bytes to contain 1024 bytes of data
  * 1 cluster is 2048 bytes and 1 reference `mbuf` is 128 bytes
* Clusters are able to be shared between multiple `mbuf`s

### Example
* `write` 4096 bytes to a TCP socket
* One cluster is filled with the first 2048 bytes by the socket layer
* Protocol's send routine is called
* TCP send routine appends `mbuf` to send buffer and calls `tcp_output`
* Assume TCP maximum segment size of 1460 bytes (typical for Ethernet)
* `tcp_output` builds a segment containing the first 1460 bytes of data
* `tcp_output` also builds an `mbuf` containing the IP and TCP headers, leaving room for a link-layer header
* `mbuf` is passed to IP output and ends up on IP's output queue
* In UDP example from Chapter 1, UDP did not keep the `mbuf` in the send buffer
* TCP is a reliable protocol and must maintain a copy of the data that it sends, until the data is acknowledged by the receiver
* `tcp_output` calls the `m_copy` function, copying the first 1460 bytes of the send buffer
* Since the data is in a cluster, `m_copy` creates an `mbuf` to point to the correct place in the existing cluster
  * Note that this cluster has 588 additional bytes of data (i.e. it's the cluster that was filled with the first 2048 bytes by the socket layer)
* This sharing of clusters means the kernel doesn't have to copy data from one `mbuf` to another
* Implemented by reference counters for each cluster
  * Reference counter is incremented each time an `mbuf` points to the cluster
  * Decremented each time a cluster is released
  * Only when the reference counter reaches 0 is the memory used by the cluster available for some other use
* When the first `mbuf` chain containing the first 1460 bytes of data reaches the Ethernet device driver, the driver releases the `mbuf` in the chain pointing to the cluster
  * Cluster reference is decremented
  * Cluster is still referenced by the `mbuf` in the TCP send buffer, as there is still remaining data that needs to be sent)
* Remaining 588 bytes in the send buffer don't comprise a full-sized segment
* Socket layer continues processing data from the application
* Remaining 2048 bytes (of the 4096 total) are placed into an `mbuf` with a cluster
* TCP send routine called again with a new `mbuf` appended to socket's send buffer
* `tcp_output` builds another `mbuf` chain and the next 1460 bytes of data
* The data comes from two clusters
  * First 588 bytes from the first cluster in the send buffer
  * Next 872 bytes are from the second cluster in the send buffer
  * `m_copy` does not copy this data - only references the existing clusters
