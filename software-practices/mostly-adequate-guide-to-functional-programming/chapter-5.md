# Chapter 5: Coding By COmposing

```javascript
const compose = (...fns) => (...args) => fns.reduceRight((res, fn) => [fn.call(null, ...res)], args)[0];
```

OR more easy to digest (for two functions)

```javascript
const compose2 = (f, g) => x => f(g(x));
```

* Note how composition of two functions returns a new function
* Also note how the `g` will run before the `f` creating a "right-to-left" flow of data

```javascript
compose(f, compose(g, h)) === compose(compose(f, g), h)
```

* Composition is associative, which means it doesn't matter how you group two of them
* One benefit of this associativity is the ability to extract a group of functions and bundle them together via composition

```javascript
const loudLastUpper = compose(exclaim, toUpperCase, head, reverse);

const last = compose(head, reverse);
const angry = compose(exclaim, toUpperCase);
const loudLastUpper = compose(angry, last);
```

* Benefit is that `last` and `angry` are now reusable
* Pointfree style means that functions never mention the data upon which they operate
  * `word.toLowerCose()` is not pointfree because the "data" is mentioned (in this case, `word`)

```javascript
// not pointfree
const snakecase = word => word.toLowerCase().replace(....);

// pointfree
const snakecase = compose(replace(...), toLowerCase);
```

* Don't need the data to construct our function in the pointfree version, whereas in the pointful one, `word` must exist before we do anything else
* Pointfree code can help remove needless names and keep things concise and generic
* However, not all functional code is pointfree and that is ok
* When debugging, a common (if impure) helper function is `trace`

```javascript
const trace = curry((tag, x) => {
  console.log(tag, x);
  return x;
});
```

* `trace` helps log what the value is at that point in time (and the value that will be passed to the next function, especially when `trace` is used in a `compose`)