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
    * Only mbuf in chain that references the next packet