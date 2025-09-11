# [How to use dig](https://jvns.ca/blog/2021/12/04/how-to-use-dig/)
* 2 types of `dig`  arguments
  * Arguments that tell `dig` what DNS query to make
  * Arguments that tell `dig` how to format the response
* Three things to control about a DNS query are
  * The `name`
  * The `DNS query type`
  * And the server to send the query to
    * Defaults to what exists in `/etc/resolv.conf` 
  * Formatted like `dig @server type name`
    * `dig @8.8.8.8 jvns.ca`  queries Google's public DNS server (`8.8.8.8`) for `jvns.ca`
    * `dig ns jvns.ca` make a query with type `NS` for `jvns.ca` 
* `IN` is the query class and stands for internet
  * Relic from 80s and 90s where other networks were competing with the internet like "chaosnet"

## Formatting Options

### Use `+noall` `+answer`
* Just prints what's in the `Answer` section of the DNS response

### Use `+short`
* Just shows the content of each record

### Use digrc
* Can create a `.digrc` file in the home directory
* Can put formatting options like `+noall +answer` in the `.digrc` file
* "Long" answer can be found by using the `+all` option

## `dig +trace`
* Mimics what a DNS resolver does when it looks up a domain
* Starts at root nameservers, queries the next level of nameservers, etc, until it reaches the authoritative nameserver for the domain
