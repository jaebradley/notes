# Chapter 7: Hindley-Milner And Me

* In `Hindley-Milner` functions are written as `a -> b` where `a` and `b` are variables for any type

```javascript
// example type signatures

// capitalize :: String -> String
const capitablize = s => toUpperCase(head(s)) + toLowerCase(tail(s));

// strLength :: String -> Number
const strLength = s => s.length

// match :: Regex -> (String -> [String])
const match = curry((reg, s) => s.match(reg));

// onHoliday :: String -> [String]
const onHoliday = match(/holiday-/ig);

// id :: a -> a
const id = x => x;

// map :: (a -> b) -> [a] -> [b]
const map = curry((f, xs) => xs.map(f))
```

* For `match`, it takes a `Regex` object and returns a function that takes a `String` and returns `[String]` (because of the currying)
* Note how `onHoliday` delegates to `match` so the regex argument it passes to `match` "pops" one type off the front of the signature, which is why it takes a `String` and returns `[String]`
* The `id` function takes any old type `a` and returns something of the same type `a` - if two types are the same variable they have to be the same type
* `a -> b` can by any type `a` to any type `b` (`b` may or may not be the same type as `a`)
* `map` can be read as "`map` takes a function from any type `a` to the same or differenty type `b`, then takes an array of `a`s and results in an array of `b`'s)

```javascript
// sort :: Ord a => [a] -> [a]
```

* `sort` -> `a` must be an `Ord` or in other words, `a` must implement the `Ord` interface