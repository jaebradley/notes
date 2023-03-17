# [`Understanding CORS`](https://medium.com/@baphemot/understanding-cors-18ad6b478e2b)

* Before `CORS` (Cross-Origin Resource Sharing) there was no way to call API endpoint under different domain for security reasons
* `CORS` is supposed to prevent attackers from making `AJAX` requests on behalf of the client to websites, and in case you were logged-in, making transactions using your credentials
* `CORS` is triggered when making an HTTP request to
  * A different domain (`example.com` calls `some-api.com`)
  * A different subdomain (`example.com` calls `api.example.com`)
  * A different port (`example.com` calls `example.com:3001`)
  * A different protocol (`http://example.com` calls `https://example.com`)
* If server does not respond with specific headers to a request that does not includes cookies, or which `Content-Type` is `application-x-www-form-urlencoded`, `multi-part/form-data` or `text-plain`, data will still be received but the browser won't allow JS to access response
* If browser attempts request that includes cookies, or `Content-Type` not previous values then `preflight` is used to make an `OPTIONS` request that is sent to the server
  * If server does not respond properly to `preflight`, then only the `preflight` call will be made - the actual HTTP request the browser meant to make will not be executed

## Headers

### `Access-Control-Allow-Origin`

* Returned by server
* Indicates which client domains are allowed to access resources
* Values are `*` for any domain or a fully qualified domain
  * If the client is required to pass authentication headers (e.g. cookies) the value **can not be** `*` - it must be a fully-qualified domain

### `Access-Control-Allow-Credentials`

* Required if server supports authentications via cookies

### `Access-Control-Allow-Headers`

* Comma-separated list of request header values the server supports
* Custom headers need to be returned in the `Access-Control-Allow-Headers` response to `OPTIONS`, otherwise the request will be blocked

### `Access-Control-Expose-Headers`

* Specifies the list of headers that will be present in the actual HTTP request, and the headers that will be available to the client
* All other headers will be restricted

### `Access-Control-Allow-Methods`

* Comma-separated list of HTTP request methods (`GET`, `POST`, etc.) which the server is willing to support

### `Origin`

* Part of the request that the client makes and contains the domain from which the application is started
* Browser's do not allow you to overwrite this value
