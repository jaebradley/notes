# Chapter 10: Applicative Functors

* Two functors of the same type, and want to call a function with both of their values as arguments - adding values of two `Container`s
* `Container.of(2).chain(two => Container.of(3).map(add(two)))`
  * `chain` calls `map` then `join`
  * `Container#map` returns a `Container` with the `map` function called with the `Container` value
  * `chain(two => Container.of(3).map(add(two)))` => `map(two => Container.of(3).map(add(two))).join()`
  * `Container.of(3).map(add(two))` => `Container.of(add(two)(3))` => `Container.of(5)`
    * Note how `add(2)` is partially-applied
  * `map(...)` => `Container.of(Container.of(5))`
  * `join`ing the result plucks out inner value `Container.of(5)`
* `ap` function applies function contents of one functor to the value contents of another
* `Container.of(add(2)).ap(Container.of(3))` => `Container(5)`
* `Container.of(2).map(add).ap(Container.of(3))` => `Container(5)`
* `ap` can be implemented by calling other `Container`'s `map` with current `Container`'s value (which is a function)

```javascript
Container.prototype.ap = function(otherContainer) {
  return otherContainer.map(this.$value);
}
```

* `F.of(x).map(f) === F.of(f).ap(F.of(x))`
  * "We can place `x` into our container and `map` `f` against it OR `f` and `x` can be put into containers and `ap`ped

## Coordination Motivation

* Two separate API calls for getting destinations and events
* Both calls are used to render a page

```javascript
const renderPage = curry((destinations, events) => doSomething);

Task.of(renderPage).ap(Http.get('/destinations')).ap(Http.get('events'));
```

* `renderPage` will be called when both `Http` calls are resolved
* `renderPage` needs to be curried so it will wait for both `Task`s to finish
  * `Task.of(renderPage).ap(Http.get('/destinations')` => `renderPage(Http.get('/destinations'))`
  * `.ap(Http.get('events'))` => `renderPage(Http.get('/destinations'))(Http.get('/events')`

## Lifting

```javascript
const liftA2 = curry((g, f1, f2) => f1.map(g).ap(f2));
```

* `liftA2(add, Maybe.of(2), Maybe.of(3))` => `Maybe.of(2).map(add).ap(Maybe.of(3))`
* `Maybe.of(2).map(add)` => `Maybe.of(add(2))`
* `Maybe.of(add(2)).ap(Maybe.of(3))` => `Maybe.of(3).map(Maybe.of(add(2)))`
* `Maybe.of(3).map(Maybe.of(add(2)))` => `Maybe.of(add(2)(3))` => `Maybe.of(5)`