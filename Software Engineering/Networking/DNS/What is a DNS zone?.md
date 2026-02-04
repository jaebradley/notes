# [What is a DNS zone?](https://www.cloudflare.com/learning/dns/glossary/dns-zone/)
* A DNS zone is a portion of the DNS namespace that is managed by a specific organization / administrator
* A DNS zone is an administrative space that allows for more granular control of DNS components, like authoritative nameservers
* A DNS zone starts at a domain within the domain name space tree where the DNS root domain is at the root of this tree
  * This DNS zone can extend into subdomains so that multiple subdomains are managed by a single entity
* Common mistake is to associate a DNS zone with a domain name or a single DNS server
* A DNS zone can contain multiple subdomains and multiple zones can exist on the same server
* DNS zones are not necessarily physically separated from one another
  * Zones are strictly used for delegating control
* A DNS zone file is a plain text file stored in a DNS server that contains an actual representation of the zone and contains all the records for every domain within the zone
  * Zone files must always start with a Start of Authority record, which contains important information including contact information for the zone administrator
