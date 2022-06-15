# [How to find a domain's authoritative nameservers](https://jvns.ca/blog/2022/01/11/how-to-find-a-domain-s-authoritative-nameserver/)

* Query a root nameserver like `a.root-servers.net` via the `dig` program like `dig @a.root-servers.net google.com`
  * Look at the returned `AUTHORITY SECTION` values and pick one of the returned TLD nameservers
* Query one of the TLD nameservers for `com.` like `e.gtld-servers.net`
  * Returned `AUTHORITY SECTION` values are the authoritative nameservers
* Query the authoritative nameservers to see what DNS records it has for `google.com`
* This process is what happens when making DNS query
