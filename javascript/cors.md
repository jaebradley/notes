# [Cross-origin Resource Sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)

* A mechanism that allows restricted resources on a web page to be requested from another domain outside the domain from which the first resource was served
* A web page may freely embed cross-origin images, stylesheets, scripts, iframes, and videos
* AJAX requests are forbidden by default by the same-origin security policy
* CORS defines a way in which a browser and server can interact to determine whether or not it is safe to allow the cross-origin request

## Details

* For AJAX requests, the specification mandates that browsers "preflight" the request, which means that they first make an `OPTIONS` request to the server to check what methods are supported by the server
* If the server does not respond with the appropriate `Access-Control-*` headers, then the request errors

### Example

* A page from `http://www.example.com` tries to access data in `service.example.com`
* Browser sends `OPTIONS` request with `Origin: http://www.example.com` header
* `service.example.com` may respond with an `Access-Control-Allow-Origin` header that indicates which origin sites are allowed
  * `Access-Control-Allow-Origin: http://www.example.com` means that only requests that originate from `http://www.example.com` are allowed
  * `Access-Control-Allow-Origin: *` would allos all domains
    * The `*` value is special because it does not allow requests to supply credentials, meaning HTTP Authentication, client-side SSL certificates, nor does it allow cookies to be sent
