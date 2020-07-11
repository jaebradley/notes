# [GraphQL Directives Are Underrated](https://blog.logrocket.com/graphql-directives-are-underrated/)

* A directive is a function that augments some other functionality
  * Defined using `@` symbol

  ```graphql
query {
  post(id: 1) {
    title
    upperCaseTitle: title @uppercase
  }
}
  ```

```json
{
  "data": {
    "post": {
      "title": "Hello world!",
      "upperCaseTitle": "HELLO WORLD!"
    }
  }
}
```

* Two types of directives - schema-type directive (like `@deprecated` to mark a field as deprecated in the schema definition) and query-type directives to decide whether to execute a sectino of a query at runtime (like `@skip` or `@include`)
* GraphQL specification uses the directive location to define where a directive can appear
  * `@skip` and `@include` can only be added to `FIELD`, `FRAGMENT_SPREAD`, or `INLINE_FRAGMENT` locations while `@deprecated` can only be added to `FIELD_DEFINITION` or `ENUM_VALUE` locations
* `@defer` and `@stream` are directives that are being worked on
  * `@defer` allows certain parts of the query to render faster, eliminating the need to wait for slower fields to compute
  * `@stream` allows the first few entries in a collection to be rendered without waiting for the entire set
