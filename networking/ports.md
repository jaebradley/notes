# Ports

* Articles
  * [Wikipedia](https://en.wikipedia.org/wiki/Port_(computer_networking))
  * [`TCP` and `UDP` Ports Explained](https://www.bleepingcomputer.com/tutorials/tcp-and-udp-ports-explained/)
  * [`TCP/IP` Ports and Sockets explained](http://www.steves-internet-guide.com/tcpip-ports-sockets/)

* A port is always associated with an IP address of a host (`localhost` or `example.com`) and the protocol type of the communication (`TCP`, or `HTTP`)
* 16-bit unsigned number
* The lowest `1024` port numbers are called well-known port numbers

* `TCP` and `UDP` transfer data using protocol data units (`PDU`s)
  * `TCP` uses a segment
  * `UDP` uses a datagram
  * Both use a header field for recording the source and destination port number
* OS networking software transmits outgoing data from all application ports onto the network and forwards arriving network packets to processes by matching the packet's IP address and port number
  * `TCP` binds a single process to a specific IP address and port combination

* `HTTP` uses port `80` and `HTTPS` uses port `443` by default
* `http://www.example.com:8080/path/` specifies that the web browser connects to port `8080` of the server

* IP address is the cable box
  * Ports are the different channels on that cable box
  * Cable company knows how to send cable to cable box based upon a unique serial number associated with box (IP Address)

* `TCP` has built in error checking and will retransmit missing packets
* `UDP` is for connection-less applications
  * No built-in error checking and **will not** retransmit missing packets
