# [Basic Journey of a Packet](https://community.broadcom.com/symantecenterprise/communities/community-home/librarydocuments/viewdocument?DocumentKey=03c598ea-e171-47c9-8035-753ccd8bb36e&CommunityKey=1ecf5f55-9545-44d6-b0f4-4e4a7f5f5e68&tab=librarydocuments)
* Example: Firefox user navigates to a specific web page
* Initial TCP/IP three-way handshake
* Web browser issues an HTTP GET to the web server hosting the web page
* Firefox makes a write request to the operating system
* The data that Firefox wants to send is copied from the application's memory space to the socket send buffer within kernel space
* When copying data to the socket buffer, TCP will fragment this data, if necessary
* Any fragmentation will occur at the TCP layer if the application uses TCP as the transport protocol
  * TCP will fragment data to ensure that the size complies with the Ethernet MTU limit of 1500 bytes
* If the application uses UDP as the transport protocol then any fragmentation will occur at the IP layer
* IP layer after the transport layer: IP header is built and all IP addresses are added
* Data link layer after IP layer: data is ready to be transmitted by physical layer (network interface card)
* Packet reaches Ethernet switch
  * Most home routers are a combination of switch and router
  * If the switch does not have a hard-coded CAM table then the switch takes note of the computer's MAC address, which is unique for each Ethernet card
  * When the packet comes back from the web server, the client computer's upstream switch will know where to send that packet to
* If the packet goes through a firewall, the firewall will note the source IP address and port, along with the destination IP address and port
* If a packet has not been logged leaving the network then it won't be allowed back into the network
* Within the router, the packet's IP private address will be converted into a routable public IP address given by an ISP that was assigned to the router
* Packet may be forwarded to multiple routers
  * Initial router will route the packet based on its own routing table information
  * Once the next router receives the same packet, it will consult its own routing table and send the packet on what it considers the best path
  * Router will change fields in the IP header like the TTL field
    * Since this field has changed, the packet checksum needs to be recomputed
* At the destination web server, the physical layer will issue an interrupt request indicating that there is data to process
* Request data is passed to the data link layer where the web server recognizes that the MAC address is its own, and passes data to the IP layer
* Data is passed from IP layer to transport layer and stored in the TCP buffer
* Receiving application reads from buffer and processes the data
