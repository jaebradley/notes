# [The Evolution of Async JavaScript: From Callbacks to Promises to async/await](https://medium.freecodecamp.org/the-evolution-of-async-javascript-from-callbacks-to-promises-to-async-await-e73b047f2f40)

* Issue with callback - inversion of control
  * Trusting whatever you're giving callback to will execute it correctly
  * So 3rd party library could break how they call specified callback, and you have no control over this
* Restaurant analogy
  * Busy restaurant, put name / phone number on list
  * You assume that restaurant will call when table opens up (this is like callback function since you have no control after you give restaurant your number)
  * Restaurant also has buzzers they give you to let you know when your table is ready
    * Buzzers have three states - `pending`, `fulfilled`, `rejected`
    * `pending` is default state - this is state when they give you buzzers
    * `fulfilled` is when buzzer flashes to indicate table is ready
    * `rejected` is when something goes wrong (like restaurant is about to close)
  * You have all control - if buzzer let's you know that table is ready, you have option to go to table or not
* `Promise` takes a callback with two args - `resolve` and `reject`
  * `resolve` is a function that when called, changes the status from `pending` to `fulfilled`
  * `reject` is a function that when called, changes the status from `pending` to `rejected`
  * When `Promise` status changes to `fulfilled`, the function that was passed to `.then` is invoked
  * When status changes to `rejected`, the function that was passed to `.catch` is invoked
