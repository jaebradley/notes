# [Deep dive in CORS: History, how it works, and best practices](https://ieftimov.com/posts/deep-dive-cors-history-how-it-works-best-practices/)
* Started with the introduction of `img` tags where the resource associated with the `img` tag is fetched
  * Cross-origin request is when the browser fetches this subresource from an origin that does not reside on the same scheme, fully qualified hostname, or port as the page
* Origin is identified by a triplicate: scheme, fully qualified hostname, and port
  * `http://example.com` and `https://example.com` are two different origins
  * By default, the `http` port is `80`, while the default `https` port is `443`
  * `https://blog.example.com:8080` would have a different port than `https://blog.example.com`
* There are other tags that can fetch subresources like `script`, `frame`, `video`, `audio`, `iframe`, `link`, `form`, etc

## Attack scenario
* Imagine that CORS did not exist and web browsers allowed cross-origin requests
* `evil.com` has a `script`
* `script` contains a request to a bank's `DELETE /account` API
* So when the page is loaded, the JavaScript is executed and an HTTP request to call the bank's `DELETE` API is executed
* To authenticate this request, the `script` would also send cookies associated with the bank's website along with this HTTP request

## Enter CORS
* Three different categories of cross-origin subresource access: cross-origin writes, cross-origin embeds, cross-origin reads
* While a browser might allow certain types of cross-origin requests, these requests may not be accepted by the server
* Cross origin writes are links, redirects, and form submission
* Cross origin embeds are subresources loaded via `script`, `link`, `img`, etc
  * Allowed, by default, by the browser
  * `iframe`'s purpose is to load a different page inside the frame, where the cross-origin framing is controlled by the `X-Frame-Options` header
* Cross origin reads are subresources loaded via `fetch` requests
  * By default, blocked by browsers
 
## Preflight requests
* `OPTIONS` request that precedes a ("complex" cross origin) `POST` request due to `application/json` `Content-Type` header
* Preflight requests execute for requests that are "complex" requests from the perspective of CORS
  * Request that uses a method other than `GET`, `POST`, HEAD`
  * Request that includes headers other than `Accept`, `Accept-Language` or `Content-Language`
  * Request that has a `Content-Type` header value other than `application/x-www-form-urlencoded`, `multipart/form-data` or `text/plain`
* Preflight response headers are `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers`, `Access-Control-Max-Age`
  * So `Access-Control-Allow-Methods: POST`, `Access-Control-Allow-Headers: Content-Type`, `Access-Control-Allow-Origin: https://google.com` means that requests to `POST /greet` with the header `Content-Type: application/json` from the origin `https://www.google.com` are accepted

## Free for all

## Keeping it in the family

## Skip cookies, if you can
