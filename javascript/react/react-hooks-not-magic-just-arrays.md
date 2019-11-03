# [React Hooks: Not Magic, Just Arrays](https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e)

* How to implement `setState`
  * Two arrays - `setters` and `state`
  * On first render, push a setter function to `setters` array and push state to `state` array
  * On subsequent arrays, iterate through values in `setters` and `state` arrays
* Each of the `setters` has a reference to the related `state` value it sets, and more specifically, to the index or "cursor" position of the `state` value in the `state` array
* If use hooks in a conditional, then the index and cursor positions change between renders

```javascript
function createSetter(cursor) {
  return function setterWithCursor(newValue) {
    state[cursor] = newValue;
  }
}

function useState(initialValue) {
  if (firstRun) {
    state.push(initialValue);
    setters.push(createSetter(cursor));
    firstRun = false;
  }

  const setter = setters[cursor];
  const value = state[cursor];

  cursor++;
  return [value, setter];
}
```

* `createSetter` uses closure to keep reference to the appropriate `cursor` value for a given `setter`
  * This ensures that after the first render, setting the value points to the correct setter
* Each subsequent call of `useState` increments the `cursor` value
