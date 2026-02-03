# [Why are NS records defined within zone files (if glue records are preferred)?](https://serverfault.com/a/624276)
* DNS term called bailiwick that refers to whether a DNS record is within the authority of a higher level label or record
* `ns1.example.com` is within the bailiwick of `example.com`
* `ns1.contoso.com` is not within the bailiwick of `example.com`
* `example.com` and `contoso.com` are both within the bailiwick of `com.`
* `com.` is within the bailiwick of `.` (the root)
* glue records are needed for referrals to nameservers inside of bailiwick
* glue records for referrals to nameservers outside of bailiwick is considered bad practice

# [What is a glue record?](https://serverfault.com/a/309626)
* Owner of DNS zone for `example.com`
* Add nameservers for `example.com` - `ns1.example.com` and `ns2.example.com`
* The top-level domain's servers will delegate to the DNS servers in the whois record, but they're within `example.com`
* They try to find `ns1.example.com`, ask the `.com` servers, and get referred back to `ns1.example.com`
* Glue records send extra information in the response to the query for the `example.com` zone and send the IP address configured for the nameserver's
