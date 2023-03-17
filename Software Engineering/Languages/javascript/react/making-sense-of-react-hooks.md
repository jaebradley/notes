# [Making Sense of React Hooks](https://medium.com/@dan_abramov/making-sense-of-react-hooks-fdbde8803889)

* React hooks are effectively functions that have React state context
* They allow abstraction of behavior like "watch mouse movements and update state"
* They also are declarative, like `useWindowWidth`, which does what it says it does

```javascript
function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth);
    window.addEventListener('resize', handleResize);
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  });

  return width;
```

* Hooks are encapsulated so that each time it's called, it gets local state within currently executing component
* So they're not a way to share state, but a way to share stateful logic
* Hooks have to be called at top level
  * Wouldn't want to define state in a conditional either
* List of Hooks kept per component
  * Move to next item in Hooks list whenever Hook is used
  * Order should be same every render
