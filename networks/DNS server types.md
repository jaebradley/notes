# [DNS server types](https://www.cloudflare.com/learning/dns/dns-server-types/)

* All DNS servers fall into one of fours categories: Recursive resolvers, root nameservers, TLD nameservers, and authoritative nameservers
  * These four DNS servers work together to complete the task of delivering the IP address for a specified domain to the cliento
* A recursive resolver is the first stop in a DNS query
  * Responds with cached data to climent or send request to root nameserver, followed by a request to a TLD nameserver, followed by request to authoritative nameserver
* There are 13 DNS root nameservers that are known to every recursive resolver
  * First stop in recursive resolver's quest for DNS records
  * Root nameserver responds to recursive resolver's query by directing the recursive resolver to a TLD nameserver, based on the extension of the domain
  * While there are 13 types of root nameservers there are multiple copies of each one across the world (`632` in total as of 10/2016)
* TLD nameserver maintains information for all domains that share a common domain extension (like `.com`, `.net`, whatever comes after last dot in a URL)
  * The TLD nameserver responds to recursive resolver by pointing to an authoritative nameserver
* Authoritative nameserver responds to recursive resolver with IP address of the server found in the DNS A record, or if the domain has a CNAME record, it will provide the recursive resolver with an alias domain, at which point the recursive resolver will have to performa a whole new DNS lookup to procure a record from an authoritative nameserver
