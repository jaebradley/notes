# HTTP/1.X

## Benefits of Keepalive Connections

* Example restricts to a maximum of one TCP connection, fetching two <4kb resources (HTML document and CSS file) each which take 40 and 20ms on the server to generate, with a 28 millisecond delay between client and server
* Each TCP connection begins with a TCP three-way handshake, which takes a full roundtrip of latency
* Another roundtrip of latency due to HTTP request and response
* Add server processing time to get total time for every request
* Adding support for HTTP keepalive eliminates the second TCP three-way handshake (when fetching the CSS file after fetching the HTML document)

## HTTP Pipelining


* Once the HTML document / first request is processed by the server, there is an entire roundtrip of latency (i.e. response propagation latency) followed, by another request propagation latency (the request for the CSS resource) during which the server is idle
* If the server could begin processing the second request immediately after it finished the first, or process both requests in parallel on multiple threads, this could cut down on the number of blocking roundtrips
* HTTP/1.X protocol does not allow data from multiple responses to be interleaved (multiplexed) on the same connection, forcing each response to be returned in full before the bytes for the next response can be transferred
  * So in the case where the client sends both the HTML request and the CSS request in parallel, but the HTML request returns first, even though the server processes both in parallel and finishes processing the CSS first (takes 20ms vs. HTML document taking 40ms) the CSS request must be buffered until the HTML response is sent
  * Once th HTML response is sent, then the CSS response can be flushed from server buffers
* A single slow response blocks all requests behind it
* When processing in parallel, servers must buffer pipelined responses, which may exhaust server resources (if a response is very large) which exposes an attack vector
* A failed response may terminate the TCP connection, which forces the client to re-request all the subsequent resources
* Some browsers support pipelining, but most have it disabled

## Using Multiple TCP Connections

* In practice, most modern browsers open up to six connections per host
* The TCP congestion window is also effectively multiplied by the number of open connections, allowing the client to circumvent the configured packet limit dictated by TCP slow-start
* There is higher CPU and memory costs on both the client and server as additional sockets consume resources, like extra memory buffers
* Limiting the maximum number of connections per host allows the browser to provide a safety check against a DDoS attack

## Measuring and Controlling Protocol Overhead

* Each browser-initiated HTTP request will carry an additional 500-800 bytes of HTTP metadata
  * This is omitting the largest offender - HTTP cookies, which are commonly used for session management
  * All of this uncompressed HTTP metadata can add up to multiple KB of protocol overhead for each and every HTTP request
* RFC 2616 does not define any limit on the size of HTTP headers, however, many servers and proxies will try to enforce an 8KB or 16KB limit
* Example shows a 15-character JSON payload wrapped in 352 bytes of HTTP headers, which are transferred as uncompressed plain text over the wire (a 96% protocol byte overhead), without any cookies

## Resource Inlining

* JavaScript and CSS code can be included directly in the page, as well as images, audio, and PDF files via the data URI scheme (`data:[mediatype][;base64],data`)
* When a resource is inlined within a page, it is part of the page and cannot be cached individually by the browser
* If the same resource is inlined across multiple pages, then the same resource will have to be transferred as part of each and every page, increasing the overall size of each page
* If the inlined resource is updated, then all pages need to be invalidated and refetched
* Base64 encoding adds ~33% overhead for non-text assets
