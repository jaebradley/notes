# [It’s always `TCP_NODELAY`. Every damn time.](https://brooker.co.za/blog/2024/05/09/nagle.html_

* TCP is used to transmit single-character messages (like keyboard presses), 41 byte packet (1 byte of data, 40 bytes of header) - 4000% overhead
* Inhibit sending new TCP segments when new outgoing data arrives from the user if any previously transmitted data has yet to be acked (Nagle’s algorithm)
* Interacts poorly with the delayed ACK TCP feature
* Nagle’s algorithm blocks sending data until ACK is received, delayed ACK is delaying ACK until a response is ready
  * Great for ensuring packets are full but not great for latency-sensitive pipelines
* If your distributed system is latency-sensitive turn off `TCP_NODELAY` (Nagle’s algorithm)
