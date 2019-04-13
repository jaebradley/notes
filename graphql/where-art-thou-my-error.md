# [Where Art Thou, My Error?](https://artsy.github.io/blog/2018/10/19/where-art-thou-my-error/)

* GraphQL sets data for multiple resources, which means that some fields may resolve successfully and some fields may fail
* This is also why using HTTP always returns a `200` status code
* Different ways of rendering partial data
  * Data that failed is part of list and other entries can be rendered just fine
  * If error should be communicated (like form validation error) then error message should be part of response
* Worth noting that Apollo made decision to reject response entirely if any GraphQL query execution error exists since clients can only assume response data is incomplete, not whether or not application can handle that case
* GraphQL spec has a `message` field which is only place to provide context around errors
* GraphQL spec also defines `extensions` key which can also hold data - Apollo Server 2.0 serializes errors into `extensions` map
  * Downside to `extensions` is that there is no built-in structure for them so there's an implicit contract between server and client to process these requests
  * Counter to GraphQL philosophy of explicitly expressing shapes of data
* Another approach is to define some error messaging on the `Response` type itself
  * But this would only really apply to mutations and not queries
  * Also, superfluous data in the error cases

```graphql
type UpdateArtworkMutationResponse {
  success: Boolean!
  message: String!
  artwork: Artwork
}
```

* An extension of the previous approach is to add an explicit `error` field to the return type (or `___Payload` type)
  * If this `error` field is not-`null` something went wrong
* Artsy takes approach of making the return type of a GraphQL operation a `union` of either the response type in the successful case or some `Error` type

```graphql
type Artwork {
  title: String!
}

type HTTPError {
  message: String!
  statusCode: Int!
}

union ArtworkOrError = Artwork | HTTPError

type Query {
  artworkOrError(id: ID!): ArtworkOrError
}

query {
  artworkOrError("mona-lisa") {
    ... on Artwork {
      title
    }
    ... on HTTPError {
      statusCode
    }
  }
}
```

* Interesting way of incorporating specific error parsing using fragments
  * Basically, idea is to pull out the expected pieces of information in certain cases (if these fields exist, there was a generic error - if these fields exist, there was an HTTP error)

```graphql
query {
  artworkOrError("mona-lisa") {
    ... on Artwork {
      title
    }
    ...GenericErrorComponent
    ...GenericHTTPErrorComponent
  }
}

fragment GenericErrorComponent on Error {
  message
}

fragment GenericHTTPErrorComponent on HTTPError {
  message
  statusCode
}
```
