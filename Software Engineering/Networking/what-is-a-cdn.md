# [What Is A CDN?](https://www.incapsula.com/cdn-guide/what-is-cdn-how-it-works.html)

* Designed to solve latency
* To minimize distance between visitors and website a CDN stores cached version of website content in multiple geographic locations, called Points of Presence (PoP)
* Each PoP contains caching servers that are responsible for content delivery
* If person in London accesses US-hosted website, it is quicker to use a PoP located in the UK than to travel across Atlantic and back

* To use CDN
  * For root domain, change A record to point to an IP in the CDN's IP ranges
  * For subdomains, modify its CNAME record to point to a CDN-provided subdomain address like foobar.cdn.com
  * DNS routes visitors to CDN instead of original server

* Round Trip Time is not influenced by file size or speed of Internet connection but rather by physical distance, number of intermediate nodes, traffic, and transmission mediums

* General formula to calculate average memory reference time
`T = (1 - hit ratio) * (access time on miss) + (access time on hit) + (secondary latency factors)`

* Least Recently Used (LRU) cache discards old items first. Implemented by assigning an age counter to each cached resource and discarding those with low counters
* Most Recently Used (MRU) cache - opposite of LRU, discards new items first

* HTTP Cache Headers mark cacheable web content and set cache durations
* `Cache-Control: max-age=3600` means that the file can be cached for no longer than an hour before it must be refected from the origin content
