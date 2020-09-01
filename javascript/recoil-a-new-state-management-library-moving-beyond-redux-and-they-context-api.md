# [Recoil: A New State Management Library Moving Beyond Redux And The Context API](https://medium.com/better-programming/recoil-a-new-state-management-library-moving-beyond-redux-and-the-context-api-63794c11b3a5)

* Problems with the Context API
  * State can only be shared by pushing it up to a common ancestor, but this might include a huge tree that will then be re-rendered
  * Context can only store a single value
* Recoil defines "atoms" that are pieces of state that components can then subscribe to (and will be re-rendered when the related atom changes)
* To access derived states from a set of atoms, use selectors, that should be pure functions
  * These selectors also have an optional `set` method that allows the ability to `get` values from other atoms or selectors and the ability to set atom values
* Another benefit of selectors is that they can handle both asynchronous and synchronous data retrieval (as long as the asynchronous data retrieval returns a `Promise`)
