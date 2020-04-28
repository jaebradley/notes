# Chapter 3: Pure Happiness With Pure Functions

* A pure function is a function that, given the same input, will always return the same output and does not have any observable side effecs
* Example of `slice` vs. `splice`
  * `slice` is pure because it returns the same output per input every time
  * `splice` will change array, which is an observable "side-effect"

```javascript
// impure
let minimum = 21;
const checkAge = age => age >= minimum;

// pure
const checkAge = age => {
  const minimum = 21;
  return age >= minimum;
}
```

* The reason the first `checkAge` is considered impure is that it depends on a mutable variable
  * Depending on system state increases cognitive load by introducing an external environment
* Examples of side-effects (a change of system state or observable interaction with the outside world that occurs during the calculation of a result)
  * Changing file system
  * Changing record in database
  * Making an HTTP request
  * Printing to screen / logging to stdout
  * Querying the DOM
* It is not that we're forbidden to use them, rather we want to contain them and run them in a controlled way
* A function is a special relationship between values - each of it's input values gives back exactly _one_ output value
  * Though each input has exactly one output, that output doesn't have to be unique per input
* One benefit of pure functions is that they can be cached by their input value (since they always return the same value)
  * So if there's a pure function that calculates the square of a number, you can cache the result of the input / output and avoid having to recalculate in the future, since the result (should) always be the same
* Another benefit of pure functions is that they're self-documenting - they specify exactly what dependencies they rely on

```javascript
// impure 
const signUp = (attrs) => {   const user = saveUser(attrs);   welcomeUser(user); }; 

// pure 
const signUp = (Db, Email, attrs) => () => {   const user = saveUser(Db, attrs);   welcomeUser(Email, user); };
```

* The pure function shows that it will use a `Db`, `Email` and `attrs`
  * The fact that these dependencies are forced to be "injected" / parameterized, means that the app is more flexible since if switched to a different db, theoretically, just need to call function with it
* Pure functions have referential transparency - which is basically, being able to replace in-line (mentally) the behavior of a function as it's being used / combined with other pure functions