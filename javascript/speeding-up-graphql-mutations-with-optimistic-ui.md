# [Speeding Up GraphQL Mutations With Optimistic UI](https://www.apollographql.com/blog/tutorial-graphql-mutations-optimistic-ui-and-store-updates-f7b6b66bf0e2)

* A lot of mutations actually include two roundtrips - one to make the mutation request, and the other to refetch data after the mutation completes
* So in mutation, define `update` property like

```javascript
mutate({
  // other configuration like variables
  update: (store, { data: { response } }) => {
    const data = store.readQuery({ query: myQuery });
    data.values.push(response);
    store.writeQuery({ query: myQuery, data })
  }
})
```

* Read the data from the cache, add the created data via the mutation to the cached data, and write the data back to cache
* To simulate a zero-latency server response, add `optimisticsResponse` to `mutate`
  * `optimisticResponse` should be the response you expect from server
  * There's no more delay between when the mutation fires and when the UI response to this mutation firing occurs (in example, adding data to a list)

```javascript
mutate{
  // other configuration
  optimisticResponse: {
    mutationName: {
      responseProperty1: responseProperty1Value,
      id: Math.round(Math.random() * -1000000),
      __typename: 'ReturnTypeName'
    }
  }
}
```

* `__typename` is important so Apollo CLient knows the type of object
* The negative id is to keep track of all optimistically created objects since all server-generated objects have a positive id