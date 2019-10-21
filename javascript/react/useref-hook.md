# [React `useRef` hook](https://medium.com/trabe/react-useref-hook-b6c9d39e2022)

```jsx
class CustomTextInput extends Component {
  textInput = createRef();
  
  focusTextInput = () => this.textInput.current.focus();

  render() {
    return (
      <>
        <input type="text" ref={this.textInput} />
      </>
    );
  }
}

function CustomTextInput() {
  const textInput = useRef();

  focusTextInput = () => textInput.current.focus();

  return (
    <>
      <input type="text" ref={textInput}
    </>
  );
};
```

* If you create a ref using `createRef` in a functional component, React will create a new instance of the ref on every re-render instead of keeping it between renders
* Class components and functional components have ways of keeping data between re-renders
  * Class components use component state (after state changes, components re-render)
  * Class components can also use instance variables - value will persist for full lifetime of component and changes won't generate a re-render
  * Functional components use hooks for the equivalent
  * `useState` for utilizing state variables
  * `useRef` for equivalent of instance variables - mutating `current` property won't cause re-render

```jsx
funciton Timer() {
  const intervalId = useRef();

  useEffect(() => {
    const id = setInterval(() => { console.log('A second has passed'); }, 1000);
    intervalRef.current = id;

    return () => clearInterval(intervalRef.current);
  });
}

const handleCancel = () => clearInterval(intervalRef.current);

// render function
```

* The interval id needs to be accessible in rest of component
  * Storing interval id in a state variable would cause component to be rendered after every state update
  * Re-render would cause new interval to be created since effect is triggered after every re-render
* Avoid setting refs during rendering
  * This is because "render phase" may be restarted by React so it has to be pure and can't have side-effects
  * Updating a ref value is a side-effect - updating a counter value that's stored in a ref could be incremented more than once since render could restart
  * Instead, update using a `useEffect` which guarantees that counter will increment on re-render
    * `useEffect(() => { counter.current = counter.current + 1; });
