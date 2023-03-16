# [Beware of Complex Filtering in GraphQL](https://graphqlme.com/2018/05/05/beware-of-complex-filtering-in-graphql/)

* Complex filtering = when query type has `where` and a bunch of operators in it
  * And looks like exposiing ORM-like queries through GraphQL
* Downsides are that it opens up API where somebody could craft large / complex queries
* Should the client be responsible for creating these queries?
  * Is this like a `REST` API that takes a string like `where='user_id=1 and comment_count > 10'`?
  * The point of the API is to abstract this detail away from client?
* Business logic doesn't live in a shared place - if you need the same data on your mobile app as the data displayed in the web app, the query needs to be written twice
* Rewrite queries you want to the type of thing you're trying to solve - so instead of the previous `where` conditions it's really "retrieve user's popular posts"
  * `popularPostsByUser(userId: 1)`