# [What Happens When You Type A URL In The Browser](https://medium.com/@maneesha.wijesinghe1/what-happens-when-you-type-an-url-in-the-browser-and-press-enter-bb0aa2449c1a)

* `maps.google.com` in the address bar of browser
* Browser checks caches for DNS record to find corresponding IP address for `maps.google.com`
  * `DNS` is a database that maintains the nam eof the website and the IP address associated with it
  * Every URL on the internet has a unique IP address assigned to it
  * `DNS` is for human-friendly navigation so that people don't have to remember what the correct IP address is for a particular website
* Browser does four cache checks
  * Checks browser cache - browser maintains a mapping of `DNS` records for a fixed duration for websites you have visited
  * Browser checks - OS cache to underlying computer OS to fetch records since OS also maintains cache of `DNS` records
  * Router cache - router maintains it's cache of `DNS` records
  * `ISP` cache - all `ISP`'s maintain their own `DNS` server that would hopefully find an IP address for the requested URL
* If URL is not in any of the caches, ISP's DNS server initiates a DNS query to find the IP address for the server that hosts `maps.google.com`
  * Search is recursive because it will continue to move from `DNS` server to `DNS` server until it finds the IP address or errors and says it was unable to find it

![#domain-architecture](https://cdn-images-1.medium.com/max/800/0*udK6jZ3PjlhjqW8U.png)

* Domain level examples
  * `"."` is root domain
  * `edu`, `org`, `gov`, `com` are all "top-level" domains
  * `openoffice.org`, `expedia.gov`, `microsoft.com` are all "second-level" domains (`openoffice`, `expedia`, `microsoft`)
  * `www.expedia.gov`, `download.microsoft.com`, `sales.microsoft.org` (`www`, `download`, `sales`) are all "third-level" domains
* Each domain level contains their own name server
  * For `maps.google.com` the `DNS` recursor will contact the root name server
  * The root name server will redirect to `.com` domain name server
  * `.com` domain name server will redirect to `google.com` name server
  * `google.com` name server will find matching IP address for `maps.google.com` in its' `DNS` records and return

* Once browser has IP address, it will build connection with server to transfer information
* Connection is established via a TCP/IP three-way handshake
  * Client sends a `SYN` (synchronize) packet to server asking for new connections
  * If server has open ports it'll acknowledge by sending a `SYN/ACK` packet
  * Client will receive the `SYN/ACK` packet and will acknowledge it by sending an `ACK` packet

* Browser displays HTML content in phases
  * First will render the bare bone HTML skeleton
  * Check HTML tags and send out `GET` requests for additional elements on page (images, CSS, JavaScript)
