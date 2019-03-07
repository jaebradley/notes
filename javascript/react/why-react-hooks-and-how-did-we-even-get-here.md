# [Why React Hooks And How Did We Even Get Here](https://medium.freecodecamp.org/why-react-hooks-and-how-did-we-even-get-here-aa5ed5dc96af)

* Imagine showing mouse position for component
* Can do this by adding an event listener to `componentDidMount` that listens for `mousemove`
  * Event handler updates state
* Duplicating this behavior means will have to rewrite the code
* Can use an HOC that has the `componentDidMount` behavior (and renders underlying child component)
* This separates underlying child component implementation from mouse tracking implementation
* Whenever this HOC is used though, a new component is created - using hooks doesn't mean that a new composed component needs to always be created
* Render props
  * Same HOC logic with `componentDidMount` + `setState` but in `render` of HOC, pass the `children` the `state`
  * Then the `children` are wrapped in a function that takes the `state` properties and returns the child components, applying any relevant state properties
  * Obvious where properties are coming from (the wrapped component is more declarative)
  * Leads to a lot of nesting
    * And sometimes nested components are dependent on parent components...but rather the shared child component at the end of all the nesting needs all the data

```jsx
<MousePosition>
  {({ x, y }) => (
    <span>
      Mouse is at {x} and {y}
    </span>
  )}
</MousePosition>
```

* Here's a hook for mouse movement

```javascript
function useMousePosition() {
  const [
    position,
    setPosition,
  ] = React.useState({
    x: 0,
    y: 0,
  });

  function handleMouseMovement(event) {
    setPosition({ x: event.clientX, y: event.clientY });
  }

  React.useEffect(() => {
    window.addEventListener('mousemove', handleMouseMovement);
    // "cleanup" function that runs after the effect
    return () => window.removeEventListener('mousemove', handleMouseMovement);
  }, []);

  return position;
}
```

* This hook can now be reused across different components
* Where the component is getting data from is clear - it's declared in the `render` function directly
* The hook eliminates the need to use multiple component lifecycle methods
* Does not lead to the same level of nesting that exists when using render props
* Hooks cannot be conditional - this makes sense considering that there aren't really conditional render props either or whether the set of render props differs from render to render
