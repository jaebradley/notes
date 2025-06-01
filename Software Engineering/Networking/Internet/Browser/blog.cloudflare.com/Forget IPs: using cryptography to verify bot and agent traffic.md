# [Forget IPs: using cryptography to verify bot and agent traffic](https://blog.cloudflare.com/web-bot-auth/)
* Cloudflare has relied on two main signals to verify legitimate web crawlers: user agents and IP addresses
  * Publishing IP address ranges is limited by the fact that the same IP address might be shared by multiple useres or services within the same company
    * IP addresses might be shared by multiple companies when the hosting infrastructure is shared
    * IP addresses may be prone to change as the underlying infrastructure changes
   
## HTTP Message Signatures
* Agent signs target origin with a public key
  * `example.com/foo/bar` means that the agent signs `example.com`
  * Public key is known to the origin
* `Signature-Input` header with values indicating
  * Validity window (`created` and `expires` timestamps)
  * Key ID indicating the key used in the signature
  * A tag that indicates the intent of the message
* `Signature-Agent` indicates the origin where the public key for the agent can be found (`signer.example.com`)
* Example `User-Agent` header indicates that the software making the request is Chrome, because it is an agent that uses an orchestrated Chrome to browse the web
  * The `User-Agent` header still contains `MyBotCrawler/1.1` as the `User-Agent` header can actually contain multiple products, in decreasing order of importance
  * If our agent is making requests via Chrome, that’s the most important product and therefore comes first.
* These signatures may add a notable amount of overhead to request processing
