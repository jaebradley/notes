# [React Hooks: What's Going To Happen To Render Props?](https://kentcdodds.com/blog/react-hooks-whats-going-to-happen-to-render-props)

* Example use of render props in `reac-toggled`

```jsx
function App() {
  return (
    <Toggle>
      {({on, toggle}) => <button onClick={toggle}>{on ? 'on' : 'off'}</button>}
    </Toggle>
  )
}
```

* Hook version

```jsx
function useToggle(initialOn = false) {
  const [on, setOn] = useState(initialOn);
  const toggle = () => setOn(!on);
  return {on, toggle};
}

function App() {
  const {on, toggle} = useToggle();
  return <button onClick={toggle}>{on ? 'on' : 'off'}</button>;
}
```

*  Render props version, but using hooks (helpful for migrating over time)

```jsx
const Toggle = ({children, ...props}) => children(useToggle(props));
```

* Still a use case for render props when still need to keep inversion of control - like for `rowRenderer` in `react-virtualized`
  * Could have a `useVirtualized` hook that _could_ accept a `rowRenderer` argument but this explicit `rowRenderer` render prop is clear as well

```jsx
<List
  rowRenderer=(({key, index, style}) => {
    // Render some component
  })
>
```