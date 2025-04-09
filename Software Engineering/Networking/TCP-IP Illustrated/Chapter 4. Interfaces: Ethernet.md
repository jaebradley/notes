# Chapter 4. Interfaces: Ethernet

## 4.2 Code Introduction

### Statistics
* `ifnet` structure contains statistics for each interface
* For IP addresses, the `if_mtu` column displays subnet and unicast addresses
* If the interface is shut down, an asterisk appears next to the name
* `if_collisions` reports the number of packet collisions
* `if_oerrors` reports the number of interface output errors
* Collisions may also be counted as errors
* `if_ierrors` reports the number of packets received with input errors
* These are also problems detected by hardware, like packets that are too short or have an invalid checksum
