# [WHY YOUR WEBSITE SHOULD BE UNDER 14KB IN SIZE](https://endtimes.dev/why-your-website-should-be-under-14kb-in-size/)

* A 14kb page can load much faster than a 15kb page (612ms faster) while the difference between a 15kb and 16kb page is trivial

## What is TCP slow start?

* Algorithm that servers use to find out how many packets it can send at a time
* When browser / client makes a connection with HTTP server, the server has no way of knowing how much data can be transmitted over the connection
  * Bandwidth = how much water can come out of a pipe per second
* Server doesn't know how much data the connection can handle so it starts by sending a small / safe amount of data (usually 10 TCP packets)
* If packets successfully reach client, the client sends back an ACK
* Increases size (2x) the number of packets that are sent next
  * Process repeats until packets are lost and server does not receive ACKs
  * Server sends packets at a slower rate

## So where does 14kB come from?

* Most HTTP servers have a slow start algorithm that sends 10 TCP packets
* Max size of a TCP packet is 1500 bytes (40 byte header)
  * 10 x (remaining) 1460 bytes = 14.6k bytes or ~14kb
