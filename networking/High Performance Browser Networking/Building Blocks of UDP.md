# Building Blocks of UDP

* A datagram is "a self-contained, independent entity of data carrying sufficient information to be routed from the source to the destination nodes without reliance on earlier exchanges between the nodes and the transporting network"

## Null Protocol Services

* The IP layer has the primary task of delivering datagrams from the source to the destination host based on their addresses
* Messages are encapsulated in IP packets, which identify the source and destination addresses
* The IP layer provides no guarantees about message delivery or notifications of failure
* If a routing node along the way drops the IP packet due congestion, high load, or for other reasons, it is the responsibility of the protcol above IP to detect it, recover, and retransmit the data
* The UDP packet structure has four additional fields - source port, destination port, length of packet, and checksum
* When the IP layer delivers the packet to the destination host, the host is able to unwrap the UDP packet, identify the target application based on the destination port, and deliver the message
* UDP is an application multiplexer in that it connects the source and target application ports between the communicating hosts
* UDP has no guarantee of message delivery (no acks, restransmissions, timeouts)
  * No guaranteed order (no packaet sequence numbers, no reordering, no head-of-line blocking)
  * No connection state tracking
  * No congestion control

## UDP and Network Address Translators

* The IP Network Address Translator was introduced in the mid-1990s to resolve the fact that there are a maximum of ~4 billion IPv4 addresses and that a unique IP address could not be allocated to every host
* NAT devices would live at the edge of the network and would maintain a table mapping local IP and port tuples to one or more globally unique public IP and port tuples
* IANA has reserved three well-known ranges for private networks (most often residing behind a NAT device)
  * To avoid routing errors and confusion, no public computer is allowed to be assigned an IP address from any of these preserved private network ranges

## Connection-State Timeouts

* In TCP, each connection has a well-defined protocol state machine, which begins with a handshake, information exchange, and then an exchange to close the connection
  * Each NAT middlebox can observe the state of the connection and create / remove routing entries as needed
* UDP has no handshake / connection termination so there is no connection state machine to monitor
* Each NAT middlebox is also responsible for figuring out when to drop a translation record, so peers could stop transmitting UDP datagrams at any point
  * UDP routing records are expired on a timer
  * Best practice for long-running sessions over UDP is to introduce bidirectional keepalive packets to periodically reset the timers for the translation records in all the NAT devices along the path
  * These bidirectional keepalive packets may also be required for TCP, even though technically, they should not be necessary. Many NAT devices apply similar timeout logic to both TCP and UDP sessions

# NAT Traversal

* The internal client is unaware of its public IP - it must first discover its public IP address if it needs to share it with a peer outside its private network
* Any packet that arrives at the public IP of a NAT devices must also have a destination port and an entry in the NAT table that can translate it to an internal destination host IP and port tuple
* If someone tries to transmit data from the public network, most likely an entry in the NAT table will not exist and the packet is dropped
  * In this way, the NAT device acts as a simple packet filter, since it has no way to automatically determine an internal route, unless configured
  * This is try for inbound connections, but not outbound connections (from an internal client application) which will establish necessary translation records from the internal network
* TURN, STUN, and ICE are various traversal techniques used to establish end-to-end connectivity between UDP peers

## STUN, TURN, and ICE

* Session Traversal Utilities for NAT (STUN) is a protocol that allows the host application to discover the presence of a network address translator on the network
  * If the NAT is present, it will obtain the allocation public IP and port tuple for the current connection
  * The protocol requires assistance from a well-known, third-party STUN server that must reside on the public network
  * Application first sends a binding request to the STUN server
  * The STUN server replies with a response that contains the public IP and and port of the client as seen from the public network
  * The application discovers its public IP and port tuple and can use this when sending application data to peers
  * The outbound binding request from the application to the STUN server establishes NAT routing entires along with path so inbound packets arriving at the public IP and port tuple can find their way back to the host application on the internal network
  * STUN defines keepalive pings to keep the NAT routing entires from timing out
* Traversal Using Relays (TURN) protocol uses a public relay to transfer data between peers
  * Both clients send an allocate request to the same TURN server
  * Once a permission negotiation is complete, both peers communicate by sending their data to the TURN server which then relays it to the other peer
  * Cost to operating the TURN server, which must have enough capacity to service all data flows
* Interactice Connectivity Establishment (ICE) protocol establish the most efficient tunnel between participants
  * Uses STUN if possible, and falls back to TURN if STUN is not possible
