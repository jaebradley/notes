# Brief History of HTTP

## HTTP/1.1: Internet Standard

* Example in article has a `GET` request for `index.html` file, as well as encoding, charset, and cookie metadata in headers (separated by newlines)
* The response is chunked, where the number of octets in the chunk is expressed as an ASCII hexadecimal number
* A `0` expresses the end of the chunked stream response
* Example in article has a following request for an icon file made on the same TCP connection
  * This is the connection keepalive in action, which allows reuse of an existing TCP connection for multiple requests to the same host
* This following request has a `Connection: close` header tha indicates the server that the connection will not be reused
  * The server can also notify the client of the intent to close the current TCP connection once the response is transferred
* Note that HTTP/1.1 changed the semantics of the HTTP protocol to use connection keepalive by default, Meaning, unless told otherwise, the server should keep the connection open by default
