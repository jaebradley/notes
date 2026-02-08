# [DNS EXPLAINED - HOW DOMAIN NAMES GET RESOLVED](https://www.bhusalmanish.com.np/blog/posts/dns-explained.html)
* `A` record maps a domain to an IPv4 address
* `AAAA` record maps a domain to an IPv6 address
  * The 4 `A`s are because IPv6 addresses are four times longer than IPv4
* `CNAME` record point a domain to another domain instead of an IP address
* `MX` (Mail Exchange) record specify delivery domains for specific email address domains
  * They are assigned a priority number (lower numbers have higher priority)

## DNS Resolution Process
* Ask client (browser) cache if `api.example.com` is cached
* Ask operating system DNS cache
* Ask router cache
* Ask ISP's DNS resolver
* Ask Root Server
  * Resolver: "Where is `api.example.com`?"
  * Root: "I don't know, but the `.com` TLD is at `192.5.6.30`"
* Ask `.com` TLD server
  * Resolver: "Where is `api.example.com`?"
  * TLD: "I don't know, but `example.com`'s nameserver is at `ns1.example.com`"
* Ask `example.com`'s Nameserver
  * Resolver: "Where is `api.example.com`?"
  * NS: "It's at `93.184.216.34`"
* Resolver caches the IP address
  * Intermediate steps also cache result
  * Eventually, `93.184.216.34` is returned to the browser and the browser caches and navigates to `api.example.com`
 
## What is a Nameserver?
* Nameserver is a the source of truth - it holds the DNS records for a domain
* When registering a domain, the domain references the nameservers that store the domain's A records, CNAME records, MX records, etc
* When buying a domain they provide default nameservers like `ns1.namecheap.com`
* Can change these nameservers to use Cloudflare (`ns1.cloudflare.com`), AWS Route 53 (`ns-1234.awsdns-56.org`), etc
* Different services that own nameservers provide different features
  * For example, Cloudflare gives free DDoS protection, CDN, etc
* Authoritative nameserver contains the actual IP address associated with a domain because it holds the actual records associated with that domain

## To see cached domains
* `chrome://net-internals/#dns`
* `sudo dscacheutil -cachedump`
  * To flush: `sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder`
