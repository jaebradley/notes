# [How To Use Events in Node.js The Right Way](https://medium.freecodecamp.org/using-events-in-node-js-the-right-way-fc50c060f23b)

* Before event-driven architecture, in order to communicate between different parts of an application, one would simply invoke the requisite method
* However, this causes coupling between these two parts of the application - by using an event-driven architecture (i.e an Observer pattern), there is less coupling
* This loose coupling can lead to increased complexity
  * It can make it harder to keep track of dependencies
  * It's particularly prone to issues when events are emitted from listeners
* Consider firing an event only when a unit of business logic finishes execution
  * For example, a user's registration might involve many different steps but from a business stand point, it's one step
  * If we want to send emails after a user's registration, it might make sense to use an event-driven approach where we fire an event when registration finishes and then we have listeners for this event that send out these emails
* Node's event architecture has objects called "emitters" (specifically, an `EventEmitter` class)
  * The emitters and the listeners should be separated - these listeners can be reused (i.e. for both of these events, send the same kind of email)
  * When multiple listeners are attached to a single event, they will be executed synchronously and in the order that they were attached

```javascript
const EventEmitter = require('events');

// Instantiate an emitter
const emitter = new EventEmitter();

// In some business logic, emit some event with a name, and some payload associated with the event
emitter.emit('some-event', payload);

// In another module, have a listener that does something with the payload
emitter.on('some-event', (payload) => {
  // Do something with the payload
});
```

* A listener should do one thing and do it well
* Avoid writing too many conditionals within a listener that decide what to do depending on the data (message) that was transmitted by the event
* If find using the payload to decide what to do, simply use a different event type
* Listeners need to be detached explicitly if it's part of an object (like a method) because if not, the object will never be GCed since a part of the object (listener) will continue to be referenced by the emitter
* So in a chat app, if we have a "new message" event listener referenced in a `ChatUser` class, if a user goes offline, we need to make sure to call `removeListener` for the "new message" event
