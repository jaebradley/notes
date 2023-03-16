# [Simple Code Reuse with React](https://blog.bitsrc.io/simple-code-reuse-with-react-hooks-432f390696bf)

* React hooks are a way to reuse code that updates state
* Component that randomly updates background randomly color
* Note how the `useState` function is used
  * It returns an array with two elements
  * The first element is the current state value
  * The second element is a function that lets you update this value
  * The second element does *not* merge old and new state together

```jsx
import React, { useState } from 'react';

const ColorBanner = (colors) => {
  const [
    color,
    setColor,
  ] = useState("red");
  const changeColor = () => {
    const colorIndex = Math.floor(Math.random() * colors.length);
    const nextColor = colors[colorIndex];
    setColor(nextColor);
  }
  return (
    <div style={{ backgroundColor: color }}>
      <button onClick={changeColor}>Change Color</button>
    </div>
  )
}
```

* Can also move the hook logic to it's own function

```jsx
import React, { useState } from 'react';

const useRandomColor = (colors, initialColor) => {
  const [
    color,
    setColor,
  ] = useState(initialColor);
  const changeColor = () => {
    const colorIndex = Math.floor(Math.random() * colors.index);
    const nextColor = colors[colorIndex];
    setColor(nextColor);
  }
  return [
    color,
    changeColor,
  ];
}

const ColorBanner = (colors) => {
  const [
    color,
    changeColor
  ] = useRandomColor(colors, "red");
  return (
    <div style={{ color }}>
      <button onChange={changeColor}>Change Color</button>
    </div>
  );
}
```

* Advantages to using hooks
  * Hooks are easy to understand as they're functions
  * No need to deal with `this`
  * Cleaner syntax
  * Granular - many small hooks that deal with own "slice" of `state` vs. dealing with large state object
