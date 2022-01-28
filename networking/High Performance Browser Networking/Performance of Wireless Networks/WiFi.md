# WiFi

* WiFi devices became popular due to the fact that WiFi operated in the unlicensed 2.4GHz ISM band allowed anyone to easily provide a wireless extension to their existing local area network

## From Ethernet to a Wireless LAN

* Ethernet and WiFi protocols treat the shared medium, regardless of whether it is a wire or radio waves as a "random access channel" which means that there is no central process, or scheduler, that controls who or which device is allowed to transmit data at any point in time
  * Each device decides on its own, and all devices must work together to guarantee good shared channel performance
* Ethernet standard has historically relied on a probabilistic Carrier Sense Multiple Access (CSMA) protocol, which operates as a "listen before you speak" algorithm
  * Check whether anyone else is transmitting
  * If channel is busy, listen until it is free
  * When channel is free, transmit data immediately
* Since it takes time to propagate any signal, collisions can still occur
  * Ethernet standard also added collision detection: if a collision is detected, both parties stop transmitting immediately and sleep for a random interval so competing senders won't restart transmissions simultaneously
* WiFi cannot detect collisions while sending data (since data is sent over radio) so WiFi relies on collision avoidance, where each sender attempts to avoid collisions by transmitting only when the channel is sensed to be idle, and then sends its full message frame in its entirety
  * Once the WiFi frame is sent, the sender waits for an explicit acknowledgement from the receiver before proceeding with the next transmission
* Channel load of < 10% produces good throughput, but if load increases then the number of collisions increases quickly leading to unstable performance of the entire network

## Measuring and Optimizing WiFi Performance

* All traffic both within your own network and in nearby WiFi networks must compete for access for the same shared radio resource
* The most widely used 2.4GHz band provides three non-overlapping 20MHz radio channels (1, 6, 11)
  * Effecitvely means that the moment you have two or three nearby WiFi networks, some must overlap and thus compete for the shared bandwidth in the same frequency ranges
* The are no guarantees for the latency of the first hop between your client and the WiFi access point
  * In environments with many overlapping networks, there may be high variability due to competition for access to a shared channel with every other wireless peer
* The 5 GHz band, used by newer standards offers a wider frequency range, and has less competition

## Packet Loss in WiFi Networks

* The probabilistic scheduling of WiFi transmissions can result in a high number of collisions between multiple wireless peers in the area
  * Does not translate to higher amounts of observed TCP packet loss
  * The data and physical layer implementations of all WiFi protocols have their own retransmission and error correction mechanisms, which hide these wireless collisions from higher layers of the networking stack
  * In other words, the absolute rate observed by TCP is often no higher than that of most wired networks. Instead, much more likely to see higher variability in packet arrival times due to underlying collisions and retransmissions performed by the lower link and physical layers

## Adaptive Bitrate Streaming

* Matches the use-case of long-lived streams like video and audio content
* A video may be encoded and stored at multiple bitrates and then segmented into many parts (i.e. a 5 second chunk in a YouTube video)
* While the client is streaming the data, the client / server can monitor the download rate of each segment and dynamically switch the bitrate of the next segment to adjust for the varying bandwidth
* In practice, many video streaming services begin the stream with a low bitrate segment to get fast playback start and then continuously adjust the bitrate of the following segmnts based on the available bandwidth
* Netflix encodes every stream in over 120 different versions to adjust for varying screen sizes and available bandwidth bitrates
