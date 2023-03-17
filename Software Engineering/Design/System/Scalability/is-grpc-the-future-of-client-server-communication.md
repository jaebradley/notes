# [Is gRPC the Future of Client-Server Communication?](https://medium.com/@EdgePress/is-grpc-the-future-of-client-server-communication-b112acf9f365)

* `RPC` - Remote Procedure Calls
* `HTTP` - Hyper-Text Transfer Protocol
  * Text-based request-response protocol
  * Text-based means that it's human-readable
* `REST` stands for REpresentational State Transfer and is based on HTTP
  * REST is stateless which means that application servers do not need to store client context between requests
  * This means that any requests can be load-balanced across many servers and multiple requests from single client don't have to be handled by a single server

## RPC System Traits

* Interface definitions
  * Names and signatures of procedures can be defined as well as the data types of arguments and return values
  * `IDL` - Interface Definition Language describe shape of data and interface but do not express business logic
* Not all use HTTP - instead using custom TCP protocol
* HTTP requests that _are_ mapped to RPCs are meant to be implementation detail

## gRPC Layers

* Lowest Layer: HTTP/2 is transport protocol
  * HTTP/2 can multiplex many parallel requests over same network connection
  * It allows full-duplex bidirectional communication
* Middle Layer: Channel
  * Maps RPC to underlying transport layer
  * A `gRPC` call consists of
    * client-provided service name and method name
    * request metadata (key-value pairs)
    * zero or more request messages
  * A `gRPC` call is complete when server provides optional response header metadata, response messages, and a trailer metadata
  * The trailer metadata indcates whether a call succeeded or failed
  * This layer contains no interface definitions - all data is bytes
* Last layer: Stub
  * This is where interface definitions are defined
    * Does a method accept exactly one request message or a stream of request messages?
    * What kind of data is in each response message and how is it encoded?

## Protocol Buffers

* IDL for describing services, methods, and messages
* Protobufs only have a role in the last `gRPC` layer (stub layer)
* Channel and transport layers are IDL-agnostic

## Streaming

* Not only does `gRPC` support streaming, it supports full-duplex bidirectional streams
* This means that the client can use a stream to upload an arbitrary amount of request data and the server can use a stream to send back an arbitrary amount of response data, all in the same RPC
  * Most request/response protocols, like HTTP 1.1 are half-duplex
  * This means that both directions cannot be used at the same time
  * A request must be fully uploaded before server can begin responding
  * Client can upload request data at the same time the server is sending back response data
  * Eliminates the need for web-sockets

## `gRPC` usage

* Client-server applications where client application runs on desktop or mobile
  * HTTP/2 improves on HTTP1.1 in both latency and network utilization
* Not all browser XHRs use HTTP/2
  * Ways to expose `gRPC` APIs as REST + JSON so that they can be consumed by browser clients
* HTTP 1.1 uses a single connection synchronously for multiple requests
  * All requests must be sent back in the order in which they were received
  * Leads to head-of-line blocking delays which may prevent later responses (which were calculated quickly) to wait for earlier responses to finish computing
  * Using many parallel connections is also resource intensive on the client and server-side
  * HTTP/2 is much less verbose due to header compression
  * HTTP/2 also supports multiplexing requests over single connection
  * Protocol buffers, unlike JSON, were designed to be compact over the wire and efficient for deserialization
