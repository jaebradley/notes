# [The Gory Details Of URL Validation](http://www.guido-flohr.net/the-gory-details-of-url-validation/)

* `https://Myself:s3cr3t@My-Company.com:8080/path/to/Search?Q=huh&L=en#Results`
  * `https://` is the `protocol`
  * `Myself` is the `username`
  * `s3cr3t` is the `password`
  * `My-Company.com` is the hostname`
  * `8080` is the `port`
  * `My-Company.com:8080` is the `host`
  * `https://My-Company.com:8080` is the `origin`
  * `/path/to/Search` is the `pathname`
  * `?Q=huh&L=en` is the `search` or `query`
  * `{ Q: "huh", L: "en" }` is the `searchParams`
  * `#Results` is the `hash` or `fragment`
* For the `path`, `query` or `fragment`, pretty much anything is allowed / little reason to reject a value
* Ports are integers in range of 1 to 65536
  * JavaScript accepts a port of zero (known as the "wildcard port") where system should use a suitable port number
* A fully-qualified domain name / hostname is made up of at least two "labels" that are separated by dots
  * `www.example.com` is made up of the "labels" `www`, `example` and `com`
  * Each "label" must consiste of `a-z`, `0-9` or `-` and first character must be alphabetic (case does not matter)

## Numerical IPv4 Addresses

* Only numbers in range of `0-255` are allowed
* Valid numerical IP addresses do not have to be decimal
  * `http://127.0.0.1` can be written `http://0177.0.0.1`, `http://0x7f.0.0.1`, or `http://0000177.0x0.0000.0x1`
* A numerical IPv4 address does not have to be expressed as a group of four integers but can be anything from one to four integers
  * `120.144.171.205` is
    * `0x78.0x90.0xab.0xcd` in quad-dotted hex notation
    * But can also be expressed as `0x7890abcd`
    * `120.144.43981` in quad-dotted decimal notation or `2022747085`
* If there are 4 groups, then it's `8` bits in each group
* If there are 3 groups, then it's `8` bits in the first two groups and `16` bits in the last group
* If there are 2 groups, the first group has `8` bits and the last group has `24` bits
* One group has `32` bits

## Numerical IPv6 Addresses

* Address needs to be wrapped in square brackets so colons do not conflict with colons used in the scheme and to separate the port
* `http://127.0.0.1` => `http://[::1]`
* Made up of eight groups of four hexadecimal digits, each of which is separated by a colon
  * Unnecessary leading zeroes can be omitted

## Top-Level Domain Checks

* `.example`, `.test`, `.localhost`, and `.invalid`, `.example.com`, `.example.net`, and `.example.org` are reserved
  * `.local` and `.onion` are "special purpose" and `.home` and `.corp` are not `IANA`-registered but sometimes used for private use
* `.arpa` is used for technical / obsolete / esoteric
  * Same for `.int`
* While these domains are technically valid, may want to reject them as unacceptable input for application
* Don't limit length of top-level domain to three characters - `.info` is officially registered, for example
* Unicode support is a client-side feature and not part of the DNS
  * Browser converts the value into US ASCII via Punycode algorithm and appends `xn--` to start of string
  * May want to enforce that hostname does not contain forbidden US-ASCII characters

## Problems with URL Probing

* It may be tempting to skip the entire parsing and validating business and send a GET request to the URL and see if it works
* Can insert `img` tag in DOM and see if image has loaded
* Vulnerable to attack vector where attacker sets the image URL to `http://192.168.0.1:8080/favicon.ico` and the server accepts it after proving URL, the attacker knows you run a web server at that address behind a firewall
  * Can reject private network IPs but attacker can try more ports on external hostname like `funny-catvideos`
