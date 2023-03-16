# Chapter 9: Monadic Onions

* The previous `of` method is not just taking place of a constructor - it's part of an important interface, the `Pointed` interface
  * A `pointed` functor is a functor with an `of` method
* `Left.of` doesn't make sense because if a type _can_ `map`, it _should_ `map`, which is why `Right.of` does make sense
  * Generally, the pattern is `of().map()` and this doesn't make sense for `Left`

## Handling Nested Monads

```javascript
const safeProp = curry((x, obj) => Maybe.of(obj[x]));
const safeHead = safeProp(0);
const firstAddressStreet = compose(
  // Returns a Maybe of Maybe of Maybe of {}
  map(map(safeProp('street'))),
  // Returns a Maybe of Maybe of {}
  map(safeHead),
  // Returns a Maybe of []
  safeProp('addresses',)
)
```

* To get to the underlying value, need to call `map` three times in a row
* Use a `join` method to to take a nested `Maybe` of the same type and basically "lift" the `Maybe` up

```javascript
const mmo = Maybe.of(Maybe.of('nunchucks'));
// Maybe(Maybe('nunchucks))

mmo.join();
// Maybe('nunchucks')
```

* Any functor which defines a `join` method, has an `of` method and obeys a few laws is a monad

```javascript
Maybe.prototype.join = function join() {
  return this.isNothing() ? Maybe.of(null) : this.$value;
}

// Previous first address street using join
const firstAddressStreet = compose(
  join,
  map(safeProp('street')),
  join,
  map(safeHead),
  safeProp('addresses'),
);
```

## Chain

* Often call `join` right after `map` - combine this into a function called `chain`
  * `chain` is also called `flatMap`

```javascript
const chain = curry((f, m) => m.map(f).join());
const chain = f => compose(join, map(f));

const firstAddressStreet = compose(
  chain(safeProp('street')),
  chain(safeHead),
  safeProp('addresses'),
);

getjSON('/authentiate', { username: 'stale', password: 'crackers' })
  .chain(user => getJSON('/friends', { user_id: user.id }));
```

* Could have written `getJSON` with `compose` and style lends itself to explicit variable assignment via closure using the infix version of `chain`
* Remember to `map` when returning a normal value and `chain` when we're returning another functor - but only if they are the same nested types