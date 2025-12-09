# [Why gRPC Uses HTTP2](https://arpitbhayani.me/blogs/grpc-http2)
* HTTP/1.1 is a text-based protocol whereas HTTP/2 is a binary protocol
* HTTP/2 introduced multiplexing
  * HTTP/1.1 each TCP connection can handle only one request at a time
  * Multiple HTTP/1.1 request involve HTTP pipelining or opening multiple TCP connections
    * Browsers limit open TCP connections to 6-8 per domain
* HTTP/2 allows multiple requests and responses to be interleaved over a single TCP connection using streams
* Each stream has a unique ID and frames can be sent for different streams without blocking each other

## gRPC's Core Requirements
* gRPC supports four types of RPC calls
  * Unary: traditional single request, single response
  * Server streaming: server sends multiple responses
  * Client streaming: client sends multiple requests
  * Bidirectional streaming: both sides stream data simultaneously
* Chat example where with HTTP/2 client and server sends data frames containing serialized chat messages to/from users using a single TCP connection
* In a microservices architecture, services often make dozens of calls to other services to fulfill a single user request
  * With HTTP/1.1 this often times means waiting for a series of requests to sequentially complete
  * With HTTP/2, the initiating service can make all of these requests concurrently over a single connection
* HTTP/1.1 sends headers as plain text with every request
  * HTTP/2's HPACK compression maintains a dynamic table of previously seen headers
  * After the first request, common headers are referenced by index rather than sent in full
  * Reduces header overhead by 85-90% in real-world applications
* HTTP/2 for gRPC benefits are most noticeable for
  * High-frequency, small payload requests
  * Concurrent requests
  * Long-lived connections: gRPC services maintain persistent connections for streaming
    * HTTP/2 connection reuse is more efficient than HTTP/1.1's connection establishment overhead

## Streaming
* HTTP/1.1 solves streaming from a log service by long polling with timeouts, chunked transfer encoding, complex client-side reconnection logic, requiring state management
* HTTP/2 solves streaming from a log service by using `DATA` frames, flow control using `WINDOW_UPDATE` frames
* HTTP/2's flow control prevents fast producers from overwhelming slow consumers
* gRPC streaming works by client opening stream with an initial window size (65kb, by default)
  * Server sends data frames up to the window limit
  * Client processes data and sends a `WINDOW_UPDATE` frame to increase the available window
  * Server sends data based on updated window
* Window size controls how much unacknowledged data is in-flight between the server and client at any moment - like a buffering limit
* Provides natural backpressure
* Both client and server can send a `WINDOW_UPDATE` frame based on rate of consumption and production

## Trade-offs
* HTTP/2 requires more memory for HPACK compression tables, stream state management, flow control windows

## Binary Serialization / Protobuf
* Protobuf schema evolution can be encapsulated in HTTP/2's headers
  * Service versions can be negotiated via headers
  * Backward compatible metadata travels efficiently
  * Feature flags can be communicated compactly
