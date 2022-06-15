# [What is a DNS CNAME record?](https://www.cloudflare.com/learning/dns/dns-records/dns-cname-record/)

* CNAME records point to a domain, never to an IP address
* A domain with a CNAME record can point to another domain with a CNAME record or a domain with an A record
* If `blog.example.com` has a CNAME record with the value `example.com`, when there is a DNS lookup for `blog.example.com` this will also trigger a DNS lookup for `example.com`
  * If `example.com` had an A record, the IP address for `example.com` would be used when DNS resolving `blog.example.com`
* Subdomains have CNAME records that point to the root domain so that if the IP address of the host changes. only the DNS A record for the root domain needs to be updated
* CNAME record does not need to resolve to the same website as the website for the underlying A record
  * Once the client hits the underlying IP address, the web server can still handle the URL accordingly
  * Web server can look at the URL, see that it is `blog.example.com` and serve the blog page rather than the home page
* CNAMEs can point to CNAMEs but this is inefficient because it requires multiple DNS lookups
