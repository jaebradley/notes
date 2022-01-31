# Primer on Web Performance

## DOM, CSSOM, and JavaScript

* The parsing of the HTML document is what constructs the Document Object Model (DOM)
* In parallel, the CSS Object Model  is constructed from the specified stylesheet rules and resources
* The DOM and CSSOM are combined to create the render tree, at which point, the browser has enough information to perform a layout and paint something to the screen
* JavaScript execution can issue a synchronous `doc.write` and block DOM parsing and construction
* Scripts can query for a computed style of any object, which means that JavaScript can also block on CSS
* DOM construction cannot proveed until JavaScript is executed and JavaScript execution cannot proveed until CSSOM is available
* The "styles at the top, scripts at the bottom" best practice is because rendering and script execution are blocked on stylesheets, so get the CSS down to the user as quickly as you can

## Analyzing the Resource Waterfall

* Example looks at the Yahoo homepage (`http://www.yahoo.com`)
* Took `683` ms to download, the browser required 52 resources fetched from 30 different hosts, adding up to 486 KB
* While the contents of the `www.yahoo.com` document are being fetched, new HTTP requests are dispatched - HTML parsing is performed incrementally, allowing the browser to discover required resources early and dispatch the necessary requests in parallel
  * The scheduling of when the resource is fetched is in large part determined by the structure of the markup
* The initial rendering of the page occurs before all the resources are fully loaded, allowing the user to begin interacting with the page while the page is being built
* The `Document Complete` event also fires before the remaining assets are loaded
* The "connection view" shows the life of each TCP connection - in the Yahoo exmaple, there are 30 connections
* The download time of each connection is a small fraction of the total latency of each connection as there are 15 DNS lookups, 30 TCP handshakes, and a lot of network latency while waiting to receive the first byte of each response

## Latency as a Performance Bottleneck

* After increasing the connection bandwidth to ~3/4 MPBS, the page load time does not see much improvement even when increasing the bandwidth to 10 MBPS
* However, decreasing the latency by 20 ms sees a linear improvement in page load times
* This is a result of the performance characteristics of the underlying TCP protocol
  * TCP handshakes, flow and congestion control, head-of-line blocking due to packet loss are all optimized for long-lived connections and bulk data transfers
  * HTTP data flow is mostly small, bursty data transfer
  * Hetwork roundtrip time is the limiting factor in TCP throughput and performance, and thus, is also the performance bottleneck for HTTP as well

## Optimizing Time to First Byte (TTFB) for Google Search

* Since the HTML document is parsed incrementally by the browser, the server can and should flush available document markup as frequently as possible - this enables the client to discover and begin fetching critical resources as soon as possible
* Google Search offers one of the best examples of the benefits of this technique: when a search request arrives, the server immediately flushes the static header of the search page prior to even analyzing the query since the header is the same for every search page
* While the client is parsing the header markup, the search query is dispatched to the search index, and the remainder of the document, which inclusdes the search results, is delivered to the user once the results are ready
