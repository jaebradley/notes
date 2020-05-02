# [Understand The Flow Of An HTTP Request](https://medium.com/better-programming/understand-the-flow-of-a-http-request-1a268ec193f0)

* To have the DNS server resolve a domain to an IP address, the client sends a request to a DNS recursor which queries multiple name servers until it resolves the query
* Whenever a domain is purchased, a name server is configured that's responsible for keeping all DNS records
  * `mydomain.com`'s name server is Cloudflare (`ns1.cloudflare.com`)
  * So whatever query comes for `mydomain.com`, `ns1.cloudflare.com` has the authority to answer it - that's why `ns1.cloudflare.com` is called the "authoritative name server"
* DNS recursor reaches authoritative name server by querying root name server to get location of all TLD (top-level domain) name server (`.com` in this case)
  * TLD name servers store locations for all authoritative name servers for domains of that TLD (so `.com` TLD name server will store location of all authoritative name servers for all `.com` domains)
  * Authoritative name server returns IP address based on records configured

```
Type | Name | Content | TTL

A | app | some.ip.address | Auto
```

* A web server (like Apache) occupy network ports and listen to those ports (`80` is default port for HTTP and `443` is default port for HTTPS)
  * Web servers can return a response by storing static files in folders based on path, or handing request to some other process like a node server

```bash
<VirtualHost *:80>

    ServerName app.mydomain.com
    DocumentRoot "/var/www/my-static-website"
    ServerAdmin admin@mydomain.com
    
</VirtualHost>
```

* If you request `app.mydomain.com/me` it will look for a folder / file in `/var/www/my-static-website` and return that file (if it exists)
  * Or if a folder exists, it will look for `index.html`

```bash
<VirtualHost *:80>

    ServerName app.mydomain.com
    ProxyPreserveHost On

    ProxyPass / http://127.0.0.1:4000/
    ProxyPassReverse / http://127.0.0.1:4000/
    
</VirtualHost>
```

* If there's another server running on port `4000` Apache can act as a proxy for that server
  * It will forward any request coming from `app.mydomain.com` to `http://127.0.0.1:4000` and will return any response to the client