# [The Web Developer's Guide to DNS](https://rjzaworski.com/2019/04/the-web-developers-guide-to-dns)

* [`dig` (domain information groper) command](https://linux.die.net/man/1/dig) to get data about a hostname
* Look for `QUESTION` and `ANSWER` section

```bash
;; QUESTION SECTION:
;pets.com.                      IN      A

;; ANSWER SECTION:
pets.com.               9708    IN      A       72.52.10.14
```

* `DNS` operations use `UDP` (User Datagram Protocol)
  * `UDP` does not have any delivery guarantees (may never be delivered, may be delivered twice)
  * It may never get a response
* Lifecycle of `DNS` request
  * `DNS` resolver tries to identify server containing `pets.com`
  * Server doesn't know so it forwards request to `DNS` nameserver
  * If nameserver doesn't know, it forwards request or asks one of web's root servers to specify nameserver responsible for `".com"`
  * `".com"` nameserver identifies authoritative nameserver responsible for `pets.com`, which provides IP address for `pets.com`
  * This resolution can be cached in upstream servers
* `DNS` lifecycle assumes that any hop for a `DNS` request may not be known
  * `UDP` may be a better fit in this case
  * > "never resolved a host? well your request probably never arrived either - better luck next time"
* Domains exist in a "zone"
* Changing `cname`s means editing resource records in the corresponding zone - the serial number increments
* Clients will evict the last cached copy of the `pets.com` zone and replace it with the newly updated changes
* Every record in `DNS` contains a `TTL`
  * **This includes `NXDOMAIN` errors (i.e domain does not exist) within a zone - they also have TTLs**
  * Goal is to avoid repeating `DNS` requests for as long as psosible
