# [Clean up your code by removing 'if-else' statements](https://medium.com/javascript-in-plain-english/clean-up-your-code-by-removing-if-else-statements-31102fe3b083)

* Imagine function that is a bunch of `if / else` statements that will execute some set of functions with some set of values based on an input value
* Could use a JS object to effectively map the input value to the values that the downstream functions would need
* Could use a `Map` instead of an object
  * `Map`s keys can be any value (including Regex) while object keys can only be string or Symbol
  * `Map`s have a `size` property which will return the number of key/value pairs whereas the equivalent value can only be derived by using `Object.keys` and then calling `length`
* Instead of executing functions based on an input value it's two input values
  * Could concat the two values together and then for each combination, execute the necessary logic
  * Could use a `Map` as the key (i.e. `{ input1: 'some value', input2: 'some other value' }`
  * If there are many combinations of `input1` and `input2` where some subset of the combinations will share logic can use Regex as `Map` keys
    * `new Map([ [/^guest_[1-4]$/, doSomething], [/^guest_5$/, doSomethingElse] ])`
