# [What is a `Promise`](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch3.md#what-is-a-promise)

* `Promise`s represent a future value
* Analogy is a receipt at a fast food restaurant
  * Receipt represents the delivery of future food
  * Reason about the future food in a "time-independent" manner - text friend saying come join me for a meal (even though, technically you haven't received meal yet)
  * At some point in future, you will hopefully trade your promise of food (i.e. the receipt) for the actual food itself
  * One other (unfortunate) outcome - food doesn't actually arrive (order is lost, or restaurant runs out of food)
  * So, even though it might sound obvious, there are essentially two outcomes for future values - either they succeed or they fail
* `Promise.then` takes two functions - the first when the `Promise` is successfully fulfilled, and the second when the `Promise` is rejected
* **Once a `Promise` is resolved, it stays that way forever - it becomes an immutable value at that point**
  * This means it can be safely observed many times and passed around many times
  * A `Promise` can only be observed asynchronously
