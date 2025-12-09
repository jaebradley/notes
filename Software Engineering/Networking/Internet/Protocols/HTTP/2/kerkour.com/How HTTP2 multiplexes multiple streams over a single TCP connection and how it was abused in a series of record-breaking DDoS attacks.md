# [How HTTP/2 multiplexes multiple streams over a single TCP connection and how it was abused in a series of record-breaking DDoS attacks](https://kerkour.com/http2-stream-multiplexing)
* HTTP/1.1 requests are processed sequentially: clients wait for a server's response before sending another request
  * Led to hacks like concatenating multiple images into a single tile, domain sharding where servers were available on multiple domains to maximize request parallelism

## HTTP/2 connection establishment
* Application-Layer Protocol Negotiation Extension of the TLS protocol
* When clients establish a TLS connection, they need to send a list of supported application-layer protocols
* Servers then decide which protocol to use and send the protocol as part of the response
* After the TLS connection is established, the clients send a set of preface bytes

## Stream multiplexing with HTTP/2 frames
* Requests and responses are split into frames
* Each frame contains a length, type, and stream identifier so that clients and servers can reconstruct streams
* There are 10 types of frames
  * `DATA` frames convey the data for requests and responses
  * `HEADERS` frames are used to open a stream
    * Can be used to start a request or response
    * Can also contain "trailers" - "headers" sent after the body
  * `RST_STREAM` terminates a stream when an occur has occurred and is not to be used for normal stream termination
  * `SETTINGS` contains configuration parameters like `SETTINGS_ENABLE_PUSH` to enable/disable server push
  * `PUSH_PROMISE` is used by servers to notify clients in advance that the server intends to push responses
  * `PING` frame measures the minimal round-trip time between peers
    * Also determines whether an idle connection is still functional
  * `GOAWAY` indicates connection shutdown
  * `WINDOW_UPDATE` is a flow-control mechanism that lets peers decide on `DATA` frame sizes
  * `CONTINUATION` frames are used to send additional `HEADERS` data that didn't fit into the previous `HEADERS` frame
