# [DNS server types](https://www.cloudflare.com/learning/dns/dns-server-types/)

## Recursive resolver

* First stop in a DNS query
* Responds with cached data or sends a request to root nameserver, followed by request to a TLD nameerver, followed by request to authoritative nameserver
* Recursive resolver will cache information received from authoritative nameserver
  * When a client requests the IP address of a domain name that was recently requested by another client, the resolver can send the client the cached information

## DNS root nameserver

* 13 types of DNS root nameservers that are all known to every recursive resolver
  * Multiple copies all over the world (`632` different servers as of `10/2016`)
* The root nameserver directs the recursive resolver to a TLD nameserver based on the domain (`.com`, `.net`, etc)

## TLD nameserver

* Maintains information for all the domain names that share a common extension (`.com`)
* There are generic top-level domains that are not country-specific and then country code top-level domains (`.uk`)
* Points queries to authoritative nameserver

## Authoritative nameserver

* Provides recursive resolver with the IP address of the server found in the DNS A record, or if the domain has a CNAME record, provide an alias domain
  * Recursive resolver will need to perform new DNS lookup for alias domain
