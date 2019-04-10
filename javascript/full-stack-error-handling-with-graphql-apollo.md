# [Full Stack Error Handling With GraphQL Apollo](https://blog.apollographql.com/full-stack-error-handling-with-graphql-apollo-5c12da407210)

* Client errors
  * Parse phase - malformed GraphQL request
  * Validation phase - inputs that failed GraphQL type checking
  * Execution phase - resource not found, bad permissions
* Apollo client has `graphQLErrors` and `networkErrors`
* `networkErrors` are those thrown outside resolver
  * Client failed to connect
  * Request middleware error
  * Error in parse / validation phase of query (string instead of int)
* `graphQLErrors` are those thrown within resolver
  * Multiple resolvers mean array of errors

```json
// Example authentication error
{
  "error": {
    "graphQLErrors": [
      {
        "message": "forbidden",
        "locations": [],
        "path": [
          "protectedAction"
        ],
        "extensions": {
          "code": "UNAUTHENTICATED",
        }
      }
    ],
    "networkError": null,
    "message": "GraphQL error: You must be logged in"
  }
}
```

* Well-defined error codes provide information about transient errors (expired tokens) and non-transient errors
