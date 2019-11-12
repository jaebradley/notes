# [How to Think About HTTP Status Codes](https://www.mnot.net/blog/2017/05/11/status_codes)

* The first digit indicates the kind of response and the other digits indicates the specific _handling_ for the response
  * `1xx` - Informational
  * `2xx` - Successful
  * `3xx` - Redirection
  * `4xx` - Client error
  * `5xx` - Server error
* `201` indicates a successful response and the `01` specialisation indicates that the request create a resource
* HTTP status codes are supposed to be _generic_
  * Specs should not say `200 OK on the /foo resource means that the widget has been ordered" or "a 404 Not Found on a widget means that the widget is back-ordered"
  * This redefines the semantics of the status codes creating a "private" version of HTTP (code smell)
  * Listing every resource and possible status codes is a **bad practice**
    * The set of status codes that a client can potentially encounter is much larger than the handful they list
  * Resources should be defined in terms of methods / behaviors

```bash
/foo+json and /bar can have it's state updated with PUT (/foo+json) and PATCH (/foo+patch+json)
```

* The status code definitions / expected responses for `PUT` and `PATCH` are already defined and well-understood so no need to enumerate them
* `POST` - can say that _any_ successful response code indicates creation
  * This way, spec is forwards-compatible with future status codes
  * Can also enumerate certain specific status codes / states
    * `201 Created` being generated with header indicating location of newly created resource
    * `POST` leading to a `409 Conflict`
* The right place to put application-specific semantics is in the body's format
  * Can also add headers

