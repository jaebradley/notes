# [Why you should use an object and not an array for lists in Redux](https://medium.com/javascript-in-plain-english/https-medium-com-javascript-in-plain-english-why-you-should-use-an-object-not-an-array-for-lists-bee4a1fbc8bd)

* Biggest benefit is when operating on a single item
  * Read, updates, or deletes that operate on a single item will need to iterate through the list
  * Object only requires some identifier as the key
* If multiple fetches are necessary to retrieve items from a backend service, need to dedupe items by iterating over old items and new items
  * Object approach just uses some key to determine what items to keep
* Don't use object approach if cannot generate a unique identier for each item
* If need an array later, generate array using a selector that iterates through object / map
