# [What is a Reverse Proxy vs. Load Balancer](https://www.nginx.com/resources/glossary/reverse-proxy-vs-load-balancer/)

* Reverse proxy accepts a request from a client, forwards the request to a server, and returns the server's response to the client
* Load balancer distributes requests from clients across a group of servers and returns the server's response to the appropriate client
* Load balancer use cases
  * Eliminating a single point of failure
  * Can detect when a server goes down and diverts requests from that server to other servers in the group
  * Session persistence - sending all the requests from a particular client to the same server
    * Allows persisting the shopping basket for an e-commerce site
* Reverse proxy use cases
  * Can still be used when single server (vs. load balancer which is used with group of servers)
  * Abstracts away internal services and serves as public interface
    * Malicious clients cannot directly access servers
    * Many reverse proxies implement DDoS protection
    * Can scale servers up and down since clients only see reverse proxy's IP address
  * Web acceleration
    * SSL termination - reverse proxies can decrypt incoming requests and encrypt server responses so servers don't have to do any of that
    * Reverse proxy can cache response for identical request increasing response time and reducing load on servers

