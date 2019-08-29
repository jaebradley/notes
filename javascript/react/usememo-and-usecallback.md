# [`useMemo` and `useCallback`](https://kentcdodds.com/blog/usememo-and-usecallback)

```javascript
const dispense = candy => {
  setCandies(allCandies => allCandies.filter(c => c !== candy));
}

const dispense = React.useCallback(candy => {
  setCandies(allCandies => allCandies.filter(c => c !== candy));
}, []);
```

* `useCallback` is worse as it's doing more work
  * Defining a function, defining an array, calling `React.useCallback` (which will be executing it's own logic)
* On second render, original `dispense` function will get garbage-collected and then new one is created
  * In `useCallback` case the original `dispense` function won't be garbage-collected and a new one is created
* `useMemo` applies memoization to any value type - accepts function that returns value
  * Function is _only_ called when value needs to be retrieved (dependencies in array change)
* Use `useMemo` or `useCallback` when having to deal with referential equality or compuatationally expensive calculations
* If dependencies array values are objects / non-primitives, then callback will be called after every render
* Can use `useCallback` and `useMemo` to define the same non-primitives to be used in a downstream component

```javascript
function Foo({bar, baz}) {
  React.useEffect(() => {
    const options = {bar, baz}
    buzz(options)
  }, [bar, baz])
  return <div>foobar</div>
}

function Blub() {
  const bar = React.useCallback(() => {}, [])
  const baz = React.useMemo(() => [1, 2, 3], [])
  return <Foo bar={bar} baz={baz} />
}
```
* Most of the time, you should not bother optimizing unnecessary rerenders

```javascript
const primes = React.useMemo(() => calculatePrimes(iterations, multiplier), [
  iterations,
  multiplier,
])
```

* Defining function calculate primes on every render, React is only calling that function when the value is needed
* React stores previous values given the inputs and returns previous value given the same previous inputs

