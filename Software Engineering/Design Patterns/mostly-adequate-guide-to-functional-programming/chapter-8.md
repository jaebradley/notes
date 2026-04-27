# Chapter 8: Tupperware

* Example first creates an object (a container) that can hold any type of value

```javascript
class Container {
  constructor(x) {
    this.$value = x;
  }

  static of(x) {
    return new Container(x);
  }
}

Container.prototype.map = function(f) {
  return Container.of(f(this.$value));
}

Container.of(2).map(two => two + 2);
// Container(4)
```

* `Container.prototype.map` is just like `Array#map` except it takes a `Container` and returns a `Container`
* In fact, can keep chaining `Container.prototype.map`s together even changing the `Container`'s `value` type in the process (`Container.of(string)` -> `Container.of(number)`)
* A Functor is a type that implements `map` and obeys some laws (it could have easily been named `Mappable`)

## Maybe

```javascript
class Maybe {
  get isNothing() {
    return this.$value === null || this.$value === undefined;
  }

  map(fn) {
    return this.isNothing ? this : Maybe.of(fn(this.$value));
  }
}
```

* `Maybe` is a lot like `Container` except it will check to see if value is `null` or `undefined` before calling the `map` function
  * If the value is `null` then it will just return itself

```javascript
// map :: Functor f => (a -> b) -> Functor a -> Functor b
const map = curry((f, anyFunctor) => anyFunctor.map(f));
```

* `Maybe` is used in functions that might fail to return a result
  * `safeHead` now explicitly makes us deal with `null` values by having to `map` to get the value inside the `Maybe` object

```javascript
const safeHead = xs => Maybe.of(xs[0]);
```

* Sometimes a `Nothing` is returned to indicate failure
  * `withdraw` will return a `Nothing` if there's not enough cash
  * Any further `map`ping will effectively "early exit" any downstream communcation since the empty `Maybe` will not run any mapped functions

```javascript
const withdraw = curry((amount, { balance }) => Maybe.of(balance >= amount ? { balance: balance - amount } : null ))
```

## `Either`

```javascript
class Either {
  static of(x) {
    return new Right(x);
  }
}

class Left extends Either {
  map(f) {
    return this;
  }
}

class Right extends Either {
  map(f) {
    return Either.of(f(this.$value));
  }
}
```

* `Left` ignores requests to `map` over it while `Right` works just like `Container` or (`Identity`)
* `Left` is generally used to embed some type of error message

```javascript
// Example using date parsing

// getAge :: Date -> User -> Either(String, Number)
const getAge = curry((now, user) => {
  const birthDate = moment(user.birthDate, 'YYYY-MM-DD');
  return birthDate.isValid() ?
    Either.of(now.diff(birthDate, 'years)) :
    new Left('Birth date could not be parsed');
})
```

* Just like `Nothing`, logic short-circuits once a `Left` is returned - unlike `Nothing`, `Left` has some type of metadata / error message

## Purifying Side-Effects

```javascript
class IO {
  static of(x) {
    return new IO(() => x);
  }

  constructor(fn) {
    this.$value = fn;
  }

  map(fn) {
    return new IO(compose(fn, this.$value));
  }
}
```

* `IO` differs from previous functors in that the value is always a function
  * By taking a function, `IO` delays impure actions (like fetching from `localStorage` by capturing it in the function wrapper)
  * `IO` can be thought of as containing the return value of the wrapped action and not the wrapper itself
  * That's why the `constructor` adds `() => x` - to avoid immediate evaluation
* Note that calling `map` on `IO` does not actually _run_ the functions, the function in `map` slowly gets tacked onto the end of the computation - functions get built-up one at a time

## Handling Asynchronous Tasks

* Use a `Task` (which is effectively a `Promise`) which takes a function that takes two parameters - `reject` and `result`
  * `Task#map` is equivalent to `Promise#then`
* Like `IO`, `Task` will wait for caller to execute - like `IO` it just builds up a composition of functions
* To run `Task`, need to call the `fork` method

```javascript
const blog = compose(map(renderPage), getJSON('/posts'));

blog({}).fork(error => renderError, page => showPage);

showSpinner();
```

* Once `fork` is called, `Task` asynchronously runs to find posts
* `showSpinner` is called immediately since `fork` does not wait for a response
* Eventually, the error page or the blog page will render depending on if `getJSON` succeeds or not