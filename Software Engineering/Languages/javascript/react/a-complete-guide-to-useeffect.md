# [A Complete Guide to `useEffect`](https://overreacted.io/a-complete-guide-to-useeffect/)
## Isolated renders

* When using a `count` variable with the `useState` hook, React doesn't have any data binding or watching
  * Instead, when `setCount(1)` is called, React calls component again - just with `count` set to `1`
  * React then updates DOM to match latest render output

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  function handleLogClick() {
    setTimeout(() => console.log(`You clicked on: ${count}`));
  }

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
       Increment
      </button>
      <button onClick={handleAlertClick}
        Log count
      </button>
    </div>
  );
}
```

* The `count` value that gets logged to the `console` will be the `count` value at the state when clicked
  * Another way to think about this is that similar to how each render has its own `count`, each render will also have its own `handleLogClick` that uses the `count` value when it was created
* **Inside any particular render, `props` and `state` forever stay the same**

```jsx
useEffect(() => { document.title = `You clicked ${count} times`; });
```

* `useEffect` is also different on every render
  * There is no data binding or watching to support `count` inside effect function nor is `count` a mutable variable such that effect always sees latest value
* React remembers the effect function provided and runs it **after flushing changes to DOM and letting the browser paint the screen**
* Even though there's a single conceptual "effect" (updating document title), a different function is executed on each render
  * Each of these functions see `props` and `state` from the particular render it belongs to
  * Effects are part of the render result (like event handlers)

```jsx
componentDidUpdate() {
  setTimeout(() => { console.log(`You clicked ${this.state.count} times`) }, 3000);
}
```

* Unlike `useEffect` that would use `count`, `this.state.count` will always point to _latest_ count rather than the one belonging to particular render
  * Can solve this by assigning `this.state.count` to variable and then using that variable in `setTimeout`
  * When want to read the latest value inside the value defined at effect creation time use `useRef`

```jsx
useEffect(() => {
   myRef.current = someValue;
  setTimeout(() => { console.log(myRef.current); }, 3000);
});
```

## Cleanup

* React only runs effects _after_ browser paint
  * Most effects don't need to block screen updates
  * **The previous effect is cleaned up _after_ the re-render with new `prop`s**
* React knows how to clean up previous effect because it knows what render it was defined in, and thus the "state" associated with that render (`prop`s, `state`, etc)

## Synchronization

* `useEffect` lets you synchronize things outside of the `React` tree according to `props` and `state`
* If an effect that behaves differently depending on whether the component renders for the first time or not, that's a code smell
  * It shouldn't matter that rendered with prop `A`, then `B`, then `C` or rendered with prop `C` immediately

## Only executing effects when appropriate

* If component re-renders due to a state change, shouldn't execute effect if the state change is unrelated (underlying effect doesn't use any updated state)
* Second argument to `useEffect` is a dependency array that tells React not to re-run effect if each of the values in the array is same between current and previous time effect ran
* 

```jsx
useEffect(() => {
  const id = setInterval(() => {
    setCount(count + 1);
  }, 1000);
  return () => clearInterval(id);
}, []);
```

* This will only increment _once_
* First render `count` is `0` so `setCount(count + 1)` will be `setCount(1)`
* Effect is never re-run because of `[]` so it will keep calling `setCount(1)` every second
* First approach:  fix dependency array to include _all_ values inside the component that are used inside the effect (so `[count]`)
  * However, this would reset and clear interval whenever `count` changes
* Second approach: change effect code so it doesn't need a value that changes more often than necessary

```jsx
useEffect(() => {
  const id = setInterval(() => { setCount(c => c + 1); }, 1000);
  return () => clearInterval(id);
}, []);
```

* use the functional updater form of `setState`
  * Effect doesn't _read_ the `count` value any more
  * `setCount` doesn't need to know about current `count` state since `React` already knows it
* When setting a state variable depends on the current value of another state variable, you might want to try replacing them both with `useReducer`
  * Reducer decouples expressing the actions that happened in component from how state updates in response to them

```jsx
useEffect(() => {
  const id = setInterval(() => { dispatch({ type: 'tick' }); }, 1000);
  return () => clearInterval(id);
}, [dispatch])
```

* React guarantees the `dispatch` function is constant throughout component lifetime
* Instead of reading state inside effect, dispatches action that specifies information about what happened
* Effect is decoupled from `count` - effect doesn't care _how_ state was updated, it just gives information about what happened
  * Reducer takes care of updating `count` / dealing with `tick` action
  * During next render, React will call reducer, and new data will be used
* Move functions into an effect, if possible
  * No longer have to consider transitive dependencies as everything is "in-scope" of effect
* Functions  defined inside a component changes on every render since they will be created every render
  * Not ideal to skip adding `getFetchUrl` in dependencies list
  * Makes it difficult to notice when changes _are_ added to data flow and can lead to bugs like "never updating interval"
  * One solution is to hoist function outside of component or wrap it in `useCallback`
  * `useCallback` solution ensures that function only changes when necessary
    * Needs to be paired with the `useCallback`ed function being added to dependencies array of `useEffect`
    * If `useCallback` dependencies are correct (i.e. if `query` variable is used, then `query` variable in dependencies array) then `useCallback` won't re-run unless dependencies change

## Race Conditions

* fetching in `componentDidUpdate` can lead to race conditions
  * If fetching first request finishes after fetching second request, state would be incorrectly overwritten
* Cancel request in cleanup function

```jsx
useEffect(() => {
  let didCancel = false;
  
  async function fetchData() {
    const article = await API.fetch(id);
    if (!didCancel) {

    }
  }
}
```
