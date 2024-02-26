# [How VPNs really work](https://medium.com/@hnasr/how-vpns-really-work-a5da843d0eb3)

## Without a VPN

* Make request to IP 1.2.3.4 (Google) from source IP address 6.6.6.6
* Local computer sends a SYN segment
* SYN segment goes into an IP packet with a destination IP of 1.2.3.4 and a source IP of 6.6.6.6
* Google replies with a SYN/ACK segment with destination IP of 6.6.6.6 and source IP of 1.2.3.4, etc
* ISP can see the IP packet you are sending between 1.2.3.4 and 6.6.6.6
* Can introspect the contents over HTTP but not HTTPS

## With a VPN

* VPN server exists on IP 3.3.3.3
* Local computer still creates a SYN packet with destination 1.2.3.4 and source IP 6.6.6.6
* VPN client running on local computer captures the IP packet
  * Client encrypts the IP packet
  * Puts the packet inside another IP packet with VPN information and the new IP packet goes to the VPN server (destination IP address 3.3.3.3 and source IP address 6.6.6.6)
* ISP sees packets going to 3.3.3.3 (instead of directly to 1.2.3.4)
* When the VPN server receives the IP packet, it unpacks and decrypts the IP packet and sees the SYN segment that was intended for destination IP 1.2.3.4 from source IP 6.6.6.6
* VPN server sends the underlying SYN IP packet to 1.2.3.4, changing the source IP address to its own (3.3.3.3)
* Google responds to 3.3.3.3 with a SYN/ACK IP packet
* VPN server knows that the SYN/ACK IP packet needs to be forwarded to 6.6.6.6 via some mapping table
  * It creates a new IP packet with source IP 3.3.3.3 and destination IP 6.6.6.6 and puts the SYN/ACK message from Google in the packet
