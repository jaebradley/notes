# [How to use JavaScript Proxies for Fun and Profit](https://medium.com/dailyjs/how-to-use-javascript-proxies-for-fun-and-profit-365579d4a9f8)

* JavaScript proxies can wrap an existing object and intercept any access to attributes or methods
* Example proxy that console logs the property name that is attempting to be accessed

```javascript
const wrap = obj => {
  return new Proxy(obj, {
    get(target, propKey) {
      console.log(`Reading prop "${propKey}"`);
      return target[propKey];
    }
  });
};

const example = { message: 'hello world' };
const wrappedExample = wrap(example);

// Output will be
// Reading prop "message"
// hello world
console.log(wrappedExample.message);
```

* Can also intercept changes to properties by implementing a `set` handler
* Can return a dynamically generated function when a method on a proxied object is called - don't even have to touch proxied object if you don't need to
* Example is building a dynamic API client that dynamically builds API requests when `getUsers` is called or `postItems` by parsing the method name
* Another use of proxies is to query data structures with more readable methods
  * `values.findWhereNameIs('Jaebaebae')`
  * Build a proxy that is able to parse method name and see if there's a matching assertion
  * Wrap the array in this proxy
* Can also monitor async functions
  * Since method calls can be intercepted, if a method returns a `Promise`, can track when `Promise` is fulfilled (i.e. when `then` is called)
  * The proxy returns a function that starts a timer, executes the original method with the original arguments (which will return a `Promise`), calls `then` on the resultant `Promise` and then in the `then`, stops the timer, and returns the result of calling the original method
  * If other side-effects are desired, like tracking what methods have resolved in a "history" array, those side-effects can be implemented in the `then` that stops the timer
