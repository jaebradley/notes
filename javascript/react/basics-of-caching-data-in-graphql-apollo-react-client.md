# [Basics of Caching Data in GraphQL Apollo React Client](https://levelup.gitconnected.com/basics-of-caching-data-in-graphql-7ce9489dac15)

* `readQuery` will never make a request to GraphQL server as it will always read from cache or else throw an error
* `query` may make a request if data doesn't exist in cache
* `writeQuery` / `writeFragment` will update data in cache and any subscriber will be impacted by these updates
* `network-only` will save response to cache later, but will always force a network request (ensures data consistency with server)
* `no-cache` will not read nor write to cache with response and it will always force a network request
* `refetchQueries` to specify one or more queriest to run after mutation is complete to refetch various parts of store that are affected by mutation
* Use `fetchMore` to handle infinite-scroll or loading more data to append to a list of data
  * The specified `variables` are merged with the ones already specified so no need to define a `limits` variable if it's already been set
* If a query requests data that already exists in the client store under a different key (like when there's a list view and a detail view i.e. getting a list of books and getting a specific book by id) can use custom resolvers to let Apollo Client know that the data might have already been requested by another query
  * This is done by defining `cacheRedirects` in the `InMemoryCache` instantiation
* `client.resetStore` asynchronously resets Apollo cache and also refetches any active queries
  * `client.clearStore` clears the store without refetching any active queries