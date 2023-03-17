# [GraphQL Tour: Variables](https://medium.com/the-graphqlhub/graphql-tour-variables-58c6abd10f56)

```graphql
query ($username: String!) {
  reddit {
    user(username: $username) {
      username
      commentKarma
      createdISO
    }
  }
}
```

* This query is saying the following
  * There is one string variable
  * The name of that variable is username
  * That variable is used to fetch data about a user
* Benefits of variables
  * Tooling is able to analyze and ensure that variables are all used and are all of the correct type
  * All variables must be used - if a variable is declared but unused, the query is invalid
* GraphQL spec doesn't say how variables should be specified, but convention is to use a separate `JSON` blob
  * An additional field in the `JSON` body that is `POST`ed to a server
* Variables can have default values
* Creating queries on the fly (i.e injecting certain strings in some cases and other strings in other cases) has several downsides
  * It removes the ability to statically validate the queries
  * It can lead to a lot of bugs for complicated queries 
  * There might be performance problems from creating a bunch of strings
* So if queries shouldn't be created on the fly, the need to be defined ahead of time, which is where things like variables and fragments become handy
