# Chapter 11: Transform Again, Naturally

```javascript
const saveComment = compose(
  map(map(map(postComment))),
  map(map(validate)),
  getValue('#comment'),
);
```

* Remember that curried `map` definition is `const map = curry((f, xs) => xs.map(f))`
* `getValue('#comment').map(value => value.map(innerValue => validate(innerValue)))`
* `getValue` returns `Task(Maybe(String))` so needs to `map` over `Task` and `maybe` to pass text to `validate`
* `validate` returns either a `ValidationError` or `String` - however, the return type of the first nested `map` is `Task(Maybe(Either(ValidationError/String)))` which is why the next nested `map` has three levels

## Type Transformations

```javascript
// maybeToTask :: Maybe a -> Task a
const maybeToTask = x => x.isNothing ? Task.rejected() : Task.of(x.$value);

// arrayToMaybe :: [a] -> Maybe a
const arrayToMaybe = x => Maybe.of(x[0]);
```

* Note how `arrayToMaybe` loses information (i.e. information about the rest of `x`'s values is lost) - this is fine as long as the value we'll `map` doesn't get lost
* Changing one functor to another (i.e. `map`ping should be consistent even after transformation)

## Isomorphic JavaScript

* Isomorphism is fancy way of saying "holds the same data"

```javascript
// promiseToTask :: Promise a b -> Task a b
const promiseToTask = x => new Task((reject, resolve) => x.then(resolve).catch(reject));

// taskToPromise :: Task a b -> Promise a b
const taskToPromise = x => new Promise((resolve, reject) => x.fork(reject, resolve));
```

* `arrayToList` and `listToArray` are also isomorphic (for example)
* However, `arrayToMaybe` is not isomorphic because it loses information since it only captures the first element
* `arrayToMaybe` / `maybeToArray` are still natural transformations since `map`ping in either direction yields the same result

```javascript
const saveComment = compose(
  chain(postComment),
  chain(eitherToTask),
  map(validate),
  chain(maybeToTask),
  getValue('#comment'),
);
```

* Previous code can be simplified by using transformations
  * The transformations `maybeToTask` and `eitherToTask` transform the functor that `Task` is holding into another `Task` - then `join` the two, lifting the value of the inner `Task` up a level