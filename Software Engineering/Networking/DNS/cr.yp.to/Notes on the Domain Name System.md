# [Notes on the Domain Name System](https://cr.yp.to/djbdns/notes.html)

## Gluelessness
* Want the address of `www.espn.tv` and already know the address of the `.tv` DNS server
* Ask the `.tv` DNS server for the address of `www.espn.tv`
* The `.tv` DNS server says that they don't know the address of `www.espn.tv` but it does know that `.espn.tv` has two DNS servers, `ns-1.disney.corp` and `ns-2.disney.corp`
* When looking up `ns-1.disney.corp` ask the `.corp` DNS server the address of `ns-1.disney.corp`
* `.corp` DNS server says they don't know, but do know that `.disney.corp` has two DNS servers `zone.espn.tv` and `night.espn.tv`
* Can't reach `espn.tv` and `disney.corp`
* If `zone.espn.tv` had been a DNS server for `.espn.tv` instead of `ns-1.disney.corp` the `.tv` server would have provided the glue for `zone.espn.tv`
  * Note that `ns1.example.com` is within `bailiwick` of `example.com` while `ns1.contoso.com` are not within the bailiwick of `example.com`
  * RFC 1034 specifically requires glue for referrals to in-bailiwick DNS servers
  * RFC 1034, 1537, and 1912 all say that glue is unnecessary for out-of-bailiwick DNS servers
  * Some DNS server implementations ignore out-of-bailiwick glue by default
  * So the above "glueless" domains `espn.tv` and `disney.corp` are following the rules, but neither are reachable

### BIND Cache Loop-less Lookup
```
espn.tv NS ns-1.disney.corp
espn.tv NS ns-2.disney.corp

disney.corp NS ns-1.disney.corp
disney.corp NS ns-2.disney.corp
```
* When looking up `www.espn.tv`, when BIND sees the glueless delegation to `ns-1.disney.corp`, it drops the `www.espn.tv` query
  * It beings a `sysquery` for `ns-1.disney.corp`, hoping to have the `ns-1.disney.corp` address cached by the time the `www.espn.tv` query is retried
  * Clients generally don't retry more than four times
  * At author's time of writing, `dnscache` allows three levels of gluelessness
* Author recommends that all DNS servers are in-bailiwick servers with glue

## DNS server selection
* A cache has a query to transmit to the `.com` servers and has a list of addresses for the `.com` servers - which server should it contact first?
* `dnscache` contacts a random server to balance load as effectively as possible
* BIND keeps track of round-trip times for queries to each server and sends queries to whichever server has the best scroe

## The five types of DNS responses
* When a cache receives a normal DNS response, it learns one of the following things
  * Query name is an alias, and I need to change the query name and try again
    * Answer section of the response contains a CNAME record for the query name and does not match the query type
  * Query name has no records answering the query, and is guaranteed to have no records of any other type
    * Response code is NXDOMAIN *and* the query name is not an alias
  * Query name has one or more records answering the query
  * Query was not answered because the server does not have an answer. Other servers need to be contacted.
    * Applies if the authority section of the response contains NS records and the authority section of the response does not contain start-of-authority (SOA) records
    * The "other servers" should be defined in the NS records in the authority section
  * The query name has no records answering the query but it may have records of another type
* RFC 1034 and RFC 1035 state that an `NXDOMAIN` guarantees the nonexistence of every subdomain of the query domain
* An `NXDOMAIN` for `b.c` implies that `a.b.c` and `z.b.c` do not exist
* If a server has records for `a.b.c` and `z.b.c` but not `b.c` the server sends a zero-records response (the last response type above) and *not* an `NXDOMAIN` response
* RFC 2308 allows `NXDOMAIN` even when the domain exists to indicate that there are no records of any type under the query name

## Truncation
* DNS packets 512 bytes or smaller can be transmitted through UDP
* DNS packets 65535 bytes or smaller can be transmitted through TCP
* DNS clients and caches begin by transmitting a query through UDP
  * Response is sent back through UDP
  * If the response does not fit into a UDP packet, it is truncated, and the TC bit is set at the beginning of the UDP package
  * Clients and caches that support TCP see the TC bit and retry their query through TCP

