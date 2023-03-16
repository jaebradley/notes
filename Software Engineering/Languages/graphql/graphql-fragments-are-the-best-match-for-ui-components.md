# [GraphQL Fragments Are The Best Match For UI Components](https://medium.com/manifoldco/graphql-fragments-are-the-best-match-for-ui-components-72b8f61c20fe)

* There might be some common user data to populate a UI component (or many UI components across an application)
  * If another field is necessary, it may necessitate modifying the set of fields fetched or the declaration of what fields to fetch in multiple places
  * Can get around this by defining a GraphQL fragment that represents exactly what fields are common between multiple queries

```graphql
fragment UserInfo on User {
  login
  company
  avatarUrl
}
```

* Anywhere these fields are used, can just use fragment by spreading it (`...UserInfo`) in a query
* Can combine fragments that each kind've represent / connected to a part of the UI
* You might have a `TweetList` component that relies on a `TweetListData` fragment, but the tweet-specific data is contained in another fragment (`TweetData`)

```graphql
fragment TweetData on Tweet {
  field1
  field2
  field3
}

fragment TweetListData on TweetList {
  tweets: {
    ...TweetData
  }
}
```

* This way, if some field was going to be displayed by the `Tweet` component, all that would need to be adjusted on the GraphQL-side of things is adding the new field to the `TweetData` fragment
  * The `Tweet` component's parent, `TweetList` does not need to know what was modified or how to support this specific modification
  * Each component is responsible for it's own data requirements via the respective fragment associated with it. Thus, similarly to how components are composed, the data requirements for those components are similarly composed via related GraphQL fragments