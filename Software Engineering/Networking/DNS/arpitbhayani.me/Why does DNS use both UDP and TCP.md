# [Why does DNS use both UDP and TCP](https://arpitbhayani.me/blogs/dns-udp-tcp)
* DNS query flow
  * Stub resolver checks local cache
  * If not cached, query is sent to recursive resolver
  * Recursive resolver may query root servers, top-level domain servers, and authoritative servers
  * Response returns through chain (most likely getting cached along the way)
* DNS primarily uses UDP on port 53
  * DNS query over UDP requires two packets
  * TCP requires a minimum of seven packets (three for handshake + query + response + two for connection teardown)
  * No connection state to manage on servers
  * UDP DNS query latency: \~20 ms via network roundtrip
  * TCP DNS query latency:
    * Connection: \~20ms (1 round trip)
    * Query/Response: \~20ms (1 round trip)
  * UDP needs around 1KB per query while TCP needs 8 KB per query and at least 1 file descriptor per connection
* A busy DNS server handling 100k queries per second
  * With UDP, each query is stateless while TCP would mean that the server would need to maintain 100k concurrent connections
* Since most DNS queries result in cache hits, overhead of TCP connection establishment would be wasteful for majority of requests
* UDP has practical limitations
  * Originally 512 bytes
    * From [RFC-1035](https://datatracker.ietf.org/doc/html/rfc1035): "Messages carried by UDP are restricted to 512 bytes (not counting the IP or UDP headers). Longer messages are truncated and the TC bit is set in the header."
  * Up to 4 KB
    * From [RFC-6891](https://datatracker.ietf.org/doc/html/rfc6891#section-4.3)[:](https://datatracker.ietf.org/doc/html/rfc6891) "A requestor MAY choose to implement a fallback to smaller advertised sizes to work around firewall or other network limitations. A requestor SHOULD choose to use a fallback mechanism that begins with a large size, such as 4096. If that fails, a fallback around the range of 1280-1410 bytes SHOULD be tried, as it has a reasonable chance to fit within a single Ethernet frame. Failing that, a requestor MAY choose a 512-byte packet, which with large answers may cause a TCP retry."
  * In the real-world may large UDP packets are dropped
* When a DNS response exceeds the negotiated UDP size limit, the server sets the truncated bit in the response header, signaling the client to retry over TCP
  * DNSSEC responses have cryptographic signatures that significantly increase response size
  * Popular services with multiple IP addresses that have many A/AAAA records trigger TCP fallback
  * CNAME chains for complex redirection scenarios
