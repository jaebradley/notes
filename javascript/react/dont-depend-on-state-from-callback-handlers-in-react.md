# [Don't Depend On State From Callback Handlers In React](https://medium.com/better-programming/dont-depend-on-state-from-callback-handlers-in-react-167241a876ff)

```jsx
function AppContent({ onChange }) {
  const [value, setValue] = React.useState('')
  
  function handleOnChange(e) {
    if (onChange) {
      onChange(function({ person, collapsed }) {
        console.log(collapsed)
        console.log(person)
        setValue(e.target.value)
      })
    }
  }
  
  return (
    <input placeholder="Your value" value={value} onChange={handleOnChange} />
  )
}

function App() {
  const [collapsed, setCollapsed] = React.useState(true)
  
  function onChange(callback) {
    const person = collapsed ? null : { name: 'Mike Gonzalez' }
    callback({ person, collapsed })
  }
  
  return (
    <div>
      <AppContent
        onChange={(cb) => {
          setCollapsed(false)
          onChange(cb)
        }}
      />
    </div>
  )
}
```

* Important thing to note is how the `onChange` in the `App` is called _after_ the `setCollapsed` sets the value to `false`
  * When it calls the `AppContent` `onChange`, the values that it `console.log`s will be `collapsed` `true` and `person` `null` even though the `setCollapsed(false)` was called
* This is because whenever React enters a new render phase, it takes a snapshot of everything that is present specific to that render phase
  * During the render phase, React effectively creates the tree of React elements which represents the tree of components at that point in time
* So `setCollapsed(false)` does cause a re-render but that render phase is at a future point in time
* So `collapsed` is `true` and `person` is `null` because the `set` and the callback with `person` and `collapsed` are happening in the same render phase, and when the render phase started, `collapsed` was `true`
  * In the next render phase, the values will be correct