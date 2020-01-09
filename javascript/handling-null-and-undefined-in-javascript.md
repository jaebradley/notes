# [Handling `null` and `undefined` in JavaScript](https://medium.com/javascript-scene/handling-null-and-undefined-in-javascript-1500c65d51ae)

* Pass inputs (from network, database, user input, etc) through hydrating function that can specify default values when the value is not set (i.e. `undefined`)
  * `null` values will not get set to default values (which is why author chooses to avoid using `null` values)
* Use a state machine / function that handles values in different states
  * Example is balance calculator that will display a placeholder value before internal state is set so that user doesn't see a default value of `$0`
  * Changing the balance is done via a `set` method
  * All internal state is encapsulated to protect from outside interference
* Stage 3 "nullish" coallescing proposal - `??` operator defines a fallback value such that if the value on the left side of it is `undefined` or `null` then it will evaluate the value on the right
* Stage 3 optional chaining proposal - use the `?.` operator instead of regular "dot" property notation to access object properties that are `null` or `undefined`
* In functional programming, the `Either` monad allows attaching two code paths on success or failure
  * Can use builtin `Promise` object to effectively operate as an `Either`

```javascript
const exists = value => value !== null || value !== undefined;

const ifExists = value => exists(value) ? Promise.resolve(value) : Promise.reject('Invalid value');

// Will log null as invalid value
ifExists(null).then(doSomething).catch(log);

// Will not log string as invalid value
ifExists('jaebaebae').then(doSomething).catch(log);
```

* A `Maybe` encapsulates an optional value
  * When value exists it is a `Just` (a `Maybe` that contains a value)
  * Or a `Nothing`
  * Basically, `Nothing`s don't really implement a `map` method (or it just returns another `Nothing`) while `Just`s do
  * The way to use arrays to represent `Maybe`s is by implementing two functions
    * One function takes a value and puts it in an array, and then removes any invalid values (like `null` or `undefined`) - this is pretty trivial for a function that takes one value but it's use will be on display when used by next method
    * Other function defines a fallback value and some time of function to apply to the value in the array
    * If no value exists in array, then the fallback is returned
    * If value exists in array, the function is `map`ped against array and the first value is returned
   * Thus, an array with no or `1` value is used to correspond to a `Maybe`
