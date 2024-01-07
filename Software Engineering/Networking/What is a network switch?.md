# [What is a network switch?](https://www.cloudflare.com/learning/network-layer/what-is-a-network-switch/)

* Network switch connects devices within a network (often a LAN) and forwards packets to and from those devices
* Unlike a router, a switch only sends data to the single destination device and NOT a network of multiple devices
  * Destination device could be a switch, router, user's computer

## Router vs. Switch

* Routers select the paths for data packets that cross networks
* Routers are necessary for an Internet connection
* Switches are only used for interconnecting devices with the same network

## Layer 2 vs. Layer 3 Switch

* Layer 2 switches forward data based on the destination device's MAC address
* Layer 3 switches forward data based on the destination IP address
* Some switches can operate at both Layer 2 and Layer 3
* Most switches are Layer 2 switches
  * Layer 2 switches connect to devices using Ethernet cables

## MAC address vs. IP address

* Every device that connects to the Internet has an IP address
* An IP address is like a mailing address
* Because there are a limited number of IPv4 addresses, devices are typically assigned new addresses when they form a new connection with a network
* A MAC address is a permanent unique identifier for a piece of hardware
* MAC addresses are used at Layer 2 and not Layer 3
  * They are not included in IP packet headers (IP operates at Layer 3)
  * MAC addresses are not part of Internet traffic and are only used within a given network

## How do network switches know the MAC addresses of the devices in their network?

* Layer 2 network switches maintain a table in memory that maps MAC addresses to the switch's Ethernet ports
  * Called the Content Addressable Memory table

### Example

* Computer A <-> Port 1
* Computer B <-> Port 2
* Computer C <-> Port 3
* All connected with Ethernet cable
* Network packet arrives for for Computer A
* Switch sees that Computer A is connected to Port 1 via the CAM table
* CAM is stored in-memory so if the switch is turned off, the table will disappear
  * Switch will need to "relearn" the table when it is rebooted

### Empty CAM Table

* Computer A sends message to Computer B
* Switch records Computer A's MAC address and the port which the message came in on
* Switch forwards Computer A's message to all other computers on the network (except Computer A)
* When Computer B replies, switch records Computer B's MAC address and port
