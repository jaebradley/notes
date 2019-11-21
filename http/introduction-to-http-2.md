# [Introduction to HTTP/2](https://developers.google.com/web/fundamentals/performance/http2)

* HTTP/1.x clients need to use multiple connections to achieve concurrency and reduce latency
* HTTP/1.x does not compress request and response headers
* HTTP/1.x does not allow effective resource prioritization
* HTTP/2 allows header field compression
* HTTP/2 allows multiple concurrent exchanges on the same connection
  * Specifically, it allows interleaving of request and response messages on the same connection
* HTTP/2 allows prioritization of requests, which lets more important requests complete more quickly

## Binary Framing Layer

* Stream - bidirectional flow of bytes within an established connection which may carry one or more messages
* Message - sequence of frames that map to a request or response message
* Frame - smallest unit of communication in HTTP/2
  * Every frame contains a frame header which at minimum identifies the steam to which the frame belongs
* All communication is performed over a single TCP connection and may contain multiple bidirectional streams
* Each stream has a unique identifier and optional priority information that is used to carry bidirectional messages
* Each message is a request or response which consists of one or more frames
* Each frame carries specific type of data like HTTP headers, message payload, etc.
  * Frames from different streams may be interleaved and reassembled based on the stream identifier in each frame's header

## Stream Prioritization

* Each stream can be assigned a weight between 1 and 256
  * Streams that share the same parent (i.e. siblings) should be associated resources in proportion to their weight
  * If Stream A has a weight of 12 and Stream B has a weight of 4, then Stream A should receive 75% of the resources (12/16) and Stream B should receive 25% of the resources (4/16)
* Each stream can define a dependency on another stream
  * This means that parent stream should be allocated resources ahead of dependents

## One connection per origin

* All HTTP/2 connections are persistent
* Most HTTP transfers are short and bursty whereas TCP is optimized for long-lived, bulk data transfers
  * More efficient use of each TCP connection
  * Fewer connections reduces memory and processing footprint from client to server

## Flow Control

* Mechanism for preventing sender from overwhelming receiver with data they may not want or be able to process
  * Client requests a large video stream, but user has paused video so client wants to pause / throttle delivery
* Each receiver may choose to set any window size for stream and entire connection
* Each receiver sets initial connection and window sizes (in bytes)
  * This limit is reduced when sender emits a `DATA` frame and incremented when a `WINDOW_UPDATE` frame is sent by receiver
* `SETTINGS` frames on HTTP/2 connection establishment sets the flow control window sizes in both directions
* In previous example, browser could fetch part of video stream, then reduce the stream flow control window to 0 to put fetch on hold, and then resume later

## Server Push

* The ability to send multiple responses for a single client request
* Use-case is that a typical web application consists of many resources, which the client discovers by examining server document like `index.html`
  * Server can just eliminate this intermediate step and push the necessary resources ahead of timed
  * Similar to inlining CSS or JS asset in-line - by inlining the resource, pushing resource to client without waiting for client to request it

### Push-Promise 101

* All server push streams are initiated via `PUSH_PROMISE` frames
  * These frames indicate that resources need to be delivered ahead of response data that requests the pushed resources
  * Client needs to know all resources which server intends to push to avoid duplicate requests
  * Simple strategy is to send all `PUSH_PROMISE` frames, which contain the HTTP headers of promised resources, ahead of parent's response
* Once client receives a `PUSH_PROMISE` frame, it has option to decline the stream
  * This option is relevant when the client already has the resource cached
  * Improvement over inlining resources, which is basically a forced push and can't be declined
