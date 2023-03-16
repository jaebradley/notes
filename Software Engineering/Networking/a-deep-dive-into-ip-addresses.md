# [A Deep Dive Into IP Addresses](https://medium.com/@User3141592/a-deep-dive-into-ip-addresses-d7729b458ba9)

* IPv4 addresses are normally expressed in dot-notation like `xxx.xxx.xxx.xxx` where `xxx` is a value from `0` to `255`
  * Can also be expressed as `4` octets (`8` bits) since `2^8` is `156`
* So `172.217.6.36` is equivalent to `10101100 11011001 00000110 00100100`
* The internet is not a single large network, but rather a collection of networks
  * IPv4 addresses are comprised of the network prefix, which identifies the network that an address belongs to, and the host identifier, which identifies the host within that network

## IPv4 Address Classification

* Class A - IP addresses where the first bit is `0`
  * This is `0.0.0.0` to `127.255.255.255`
  * First `8` bits represent the network prefix and the rest represent the host identifier
  * `127.42.13.69` has a network prefix of `127` and a host identifier of `42.13.69`
* Class B - IP addresses where the first two bits are `10`
  * `128.0.0.0` to `191.255.255.255`
  * The first 16 bits repesent the network prefix and the rest represent the host identifier
  * `129.42.13.69` has a network prefix of `129.42` and a host identifier of `13.69`
* Class C - IP addresses are in this class if their first thre bits are `110`
  * `192.0.0.0` to `223.255.255.255`
  * The first 24 bits represent the network prefix and the remaining bits represent the host identifier
* Class D - IP addresses are in this class if their first four bits are `1110`
  * 224.0.0.0.0` to `239.255.255.255`
  * These addresses are used for multi-casting protocols (when a single packet can be sent to multiple hosts in one action)
* IPv4 address range `127.0.0.0` to `127.255.255.255` is reserved for "looping back" which is when the host sends a network request to itself
* `10.0.0.0` - `10.xxx.xxx.xxx`, `172.16.0.0` - `172.31.xxx.xxx` and `192.168.0.0` - `192.168.xxx.xxx` are private network addresses
  * Must go through the Network Address Translation protocol to connect to the internet
