# [Serving Over HTTP](https://graphql.org/learn/serving-over-http/)

* HTTP server should handle `GET` and `POST` methods
* `GET` requests would look like
  * `http://myapi/graphql?query={me{name}}`
  * Variables are sent as `JSON`-encoded string in additional query parameter called `variables`
* `POST` requests would...
  * Have `application/json` content type header
  * Have the following structure (`operationName` and `variables` are optional fields)
  * `operationName` _is_ required if multiple operations are present in query
* Recommended additional use cases
  * If the `query` query string parameter is _ever_ present, it should be parsed and handled in the same way as the `GET` case
  * If the content type is `application/graphql` then the `POST` body should be treated as GraphQL query string
* The response should _always_ be returned as `JSON`
* This `JSON` object should only show the `errors` field when errors were present on the response
* If no data is returned, the `data` field should only be included if the error occurred during execution

