# [From Zero To GraphQL Subscriptions](https://hackernoon.com/from-zero-to-graphql-subscriptions-416b9e0284f3)

* Building email client
  * On application open, query for all emails
  * When new emails are received, add it to list of all emails and display all relevant metadata
* The first feature can be satisfied using a basic GraphQL query
* The second feature can use GraphQL subscriptions
  * Server exposes pub/sub functionality

```javascript
// Server-side
newEmails.subscribe(context, onPublish: (newEmailId) => {
  // GraphQL query to fetch all related fields for new email
});

// When new emails are detected...
newEmails.publish(contextUserId, newEmailId);
```

* Better to put this logic on server than client
* If on client, imagine case of mobile app where small percentage of users may never upgrade
* If all this pub/sub behavior was on the client, it could lead to breakages between client versions
* If the server was responsible for this pub/sub system
  * Client could send server a document specifying a GraphQL document specifying a subscription for a given event and the fields it'd want back
  * Server would then keep track of this document
  * When trigger events would occur, server would return result of query applied to new trigger event

```javascript
// Subscription might look something like this

subscription SubscribeToNewEmails($viewer: ID!) {
  newEmail(viewer: $viewer) {
    receiveTime,
    sender,
    subject
  }
}
```

* From GraphQL spec

> If the operation is a subscription, the result is an event stream called the "Response Stream", where each event in the event stream is the result of executing the operation for each new event on an underlying "Source Stream".
> Executing a subscription on the server creates a persistent function that maps an underlying "Source Stream" to a returned "Response Stream"

* In the prior example, the "Source Stream" is the new emails pubsub event
* The "persistent function on the server" remembers the GraphQL query and listens to incoming email events
* Each time a new email event comes in, the mapping function executes the stored GraphQL document using the Source Stream event data
