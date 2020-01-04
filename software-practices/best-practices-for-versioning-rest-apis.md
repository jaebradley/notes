# [Best Practices for Versioning REST APIs](https://medium.com/better-programming/best-practices-for-versioning-an-api-for-rest-apis-530a9398f311)

* Three ways of versioning
  * Via URL or route (e.g. `/v1/some/route`)
  * Via a custom header (e.g. `Accept-Version: v2`)
  * Via a query string parameter (e.g. `/some/route?version=v2)
* Best practice is specifying the version in the route - only downside is that it adds some length to the REST API route
* When using a custom header, server can parse incoming payload on the fly and know how to respond appropriately when sending back data
  * Downsides are that there's additional overhead when parsing incoming payload as well as not the friendliest user interface
* Query string versioning is generally a bad practice and it indicates that required parameters and thought process for developing a schema were afterthoughts

