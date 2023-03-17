# [How we built Pingora, the proxy that connects Cloudflare to the Internet](https://blog.cloudflare.com/how-we-built-pingora-the-proxy-that-connects-cloudflare-to-the-internet/)

## Architecture limitations hurt performance

* In NGINX, each request can only be served by a single worker
  * Can lead to unbalanced load across CPUs
* Requests that do CPU-heavy or blocking IO tasks can slow down other requests
* Connection reuse speeds up time to first byte for requests since the TCP and TLS handshakes can be skipped
* The NGINX connection pool is per worker, so when a request lands on a certain worker, it can only reuse the connections within that worker
  * When more NGINX workers are added to scale up, the connection reuse ratio gets worse, because the connections are scattered across more isolated pools of all the processes

## Pingora is faster in production

* Pingora uses multithreading over multiprocessing in order to share resources, especially connection pools, easily
* Pingora only makes a third as many new connections per second compared to the previous NGINX-based service
* The Rust implementation runs more efficiently than the previous Lua implementation
  * To access an HTTP header, have to read from NGINX C struct, allocate Lua string, and then copy the header to the Lua string, and then garbage-collect the string
  * In Rust / Pingora, direct string access
* NGNIX has shared memory, but every shared memory access has to use a mutex lock, whereas in Pingora / Rust, most shared items can be directly accessed via shared references behind atomic reference counters
