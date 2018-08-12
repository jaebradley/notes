# [Deep Dive Into Websockets And HTTP/2](https://blog.sessionstack.com/how-javascript-works-deep-dive-into-websockets-and-http-2-with-sse-how-to-pick-the-right-path-584e6b8e3bf7)

* One of the most common hacks to create illusion that server is sending data back to client is long-polling
  * Every 10 seconds make a request
  * Long-polling carries overhead of HTTP, which isn't good for low latency applications

## WebSocket Handshake

* Can use [`websocket`](https://github.com/theturtle32/WebSocket-Node) library
* Client sends "regular" HTTP request to server
  * Includes `Upgrade` Header which lets the server know that client wishes to establish a WebSocket connection

```text
GET ws://websocket.example.com/ HTTP/1.1
Origin: http://example.com
Connection: Upgrade
Host: websocket.example.com
Upgrade: websocket
```

```javascript
// Instantiate WebSocket on client

// ws scheme: default port 80
var socket = new WebSocket('ws://websocket.example.com');
// wss scheme (secure WebSocket connection): default port 443 (TLS encryption)
var socket = new WebSocket('wss://websocket.example.com');
```

* After server establishes connection, server replies by upgrading
  * On the client, `open` event is fired on WebSocket instance
  * HTTP connection is replaced by WebSocket connection that uses underlying TCP connection
  * Bidirectional message sending

```text
HTTP/1.1 101 Switching Protocols
Date: Wed, 25 Oct 2017 10:07:34 GMT
Connection: Upgrade
Upgrade: WebSocket
```

## WebSocket Messages

* Data is transferred via WebSockets via messages
* Messages consist of one or more Frames
  * Frames are prefixed by 4-12 bytes about the payload - this allows for proper reconstruction when frames reach the client
  * Frames reduce the amount of non-payload data leading to significant reductions in latency
  * Client is only notified about a new message when all frames have been properly pieced back together

## Framing Protocol

* Each frame has a header
* `fin` (`1 bit`) - indicates if the frame is the final frame. Firefox makes second frame after `32k`.
* `opcode` (`4 bits`)
  * `0x00` - continues payload from previous
  * `0x01` - includes text data
  * `0x02` - includes binary data
  * `0x08` - terminates the connection
  * `0x09` - frame is a ping
  * `0x0a` - frame is a pong
* `payload_len` (`7 bits`) - length of the payload

## Fragmentation

* How frames are joined
  * Receive the first frame
  * Remember `opcode`
  * Concatenate frame payloads together until `fin` bit is set
  * Assert that the `opcode` for each package is zero
* Fragmentation is useful for splitting messages into reasonable sizes

## HTTP/2

* Introduces Server Push which enables server to proactively send resources to the client cache
* Server Sent Events(SSE) is a mechanism that allows the server to asynchronously push the data to the client
* Server then decides to send data whenever a new chunk is available (one-way Pub/Sub)
* Several SSE streams can be interleaved into a single TCP connection
  * This includes server-to-client and client-to-server


