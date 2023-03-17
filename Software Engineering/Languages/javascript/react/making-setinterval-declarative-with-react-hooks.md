# [Making setInterval Declarative with React Hooks](https://overreacted.io/making-setinterval-declarative-with-react-hooks/)

```javascript
function useInterval(callback, delay) {
  const savedCallback = useRef();

  // Remember the latest callback
  useEffect(() => {
    savedCallback.current = callback;
  }, [callback]);

  // Set up interval including adding unsubscribe function
  useEffect(() => {
    function tick() {
      savedCallback.current();
    }

    if (delay !== null) {
      let id = setInterval(tick, delay);
      return () => clearInterval(id);
    }
  }, [delay]);
}
```

```jsx
class Counter extends React.Component {
  state = {
    count: 0,
    delay: 1000,
  }:

  componentDidMount() {
    this.interval = setInterval(this.tick, this.state.delay);
  }

  componentDidUpdate(prevProps, prevState) {
    if (prevState.delay !== this.state.delay) {
      clearInterval(this.interval);
      this.interval = setInterval(this.tick, this.state.delay);
    }
  }

  componentWillUnmount() {
    clearInterval(this.interval();
  }

  tick = () => {
    this.setState({
      count: this.state.count + 1,
    });
  }

  // Render, etc.
}
```

```javascript
useEffect(() => {
  let id = setInterval(() => {
   setCount(count + 1);
  }, 1000);
  return () => clearInterval(id);
});
```

* React re-applies effects after every render by default
* If re-render too often (and effects get re-applied too often) the interval never gets a chance to execute
* Instead, specify dependency array as second argument, and React will only run effect if something in that array changes
* When only want to run effect on mount and cleanup on unmount, pass an empty `[]`

```javascript
useEffect(() => {
  let id = setInterval(() => {
    setCount(count + 1);
  }, 1000);
  return () => clearInterval(id);
}, []);
```

* This will always update counter to `1`
* `useEffect` captures count from first render
* Effect never gets re-applied so closure in `setInterval` always references count from first render and thus `count + 1` is always `1`

## Using refs

* The problem is
  * Do `setInterval(callback, delay)` with callback from first render
  * Have a callback from next sencond
  * Can't replace existing interval without resetting time
  * Don't replace interval, just point to some interval callback reference that changes over time
* `setInterval(fn, delay)` where `fn` refers to some callback
* Set callback to some callback after first render
* Set callback to some callback after second render
* This callback needs to persist across re-renders so it can't be regular variable - instance field
* `useRef` returns plain object with mutable `current` property that's shared between renders
* Can save latest interval callback to it

```javascript
function callback() {
  // Can read fresh props, state, etc.
  setCount(count + 1);
}

// After every render, save the latest callback into ref
useEffect(() => {
  savedCallback.current = callback;
});


// [] means that effect never re-rexecutes
// Which means that interval never gets reset
// But the `savedCallback` ref means that always read callback from last render
// And call it on interval tick
useEffect(() => {
  function tick() {
    savedCallback.current();
  });

  let id = setInterval(tick, 1000);
  return () => clearInterval(id);
}, []);
```

## Pausing Interval

* Pass in a `delay` that is `null` to pause interval
* Then only call `setInterval` if `delay` is non-`null`

```javascript
useEffect(() => {
  function tick() {
    savedCallback.current();
  }

  if (delay !== null) {
    let id = setInterval(tick, delay);
    return () => clearInterval(id);
  }
}, [delay]);
```
