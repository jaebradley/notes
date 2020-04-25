# [Under The Hood Of React's Hooks System](https://medium.com/the-guild/under-the-hood-of-reacts-hooks-system-eb59638c9dba)

* The dispatcher is an object that contains the hook functions
  * It is dynamically set / modified based on rendering phase of `ReactDOM`
  * When rendering is done, the dispatcher is set to `null` to avoid hooks from being used outside `ReactDOM`'s rendering cycle
* Hooks are represented as nodes that linked together by their calling order
* Hooks initial state is created in the initial render
* React needs to remember the hook's state for future renders
* React needs to provide right state based on calling order
* React needs to know what fiber a hook belongs to
  
```javascript
// How to think about React state when using hooks
{
  memoizedState: 'first',
  next: {
    memoizedState: 'second',
    next: {
      memoizedState: 'third',
      next: null
    }
  }
}
```

* Before each and every functional Component invocation, a function called `prepareHooks` is called
* When an update finishes, `finishHooks` is called, where reference to first node in hooks queue is stored on fiber in the `memoizedState` property
  * Can actually see the different states from a component's hook by iterating through the `memoizedState` values

```jsx
const ChildComponent = () => {
  useState('foo')
  useState('bar')
  useState('baz')

  return null
}

const ParentComponent = () => {
  const childFiberRef = useRef()

  useEffect(() => {
    let hookNode = childFiberRef.current.memoizedState

    assert(hookNode.memoizedState, 'foo')
    hookNode = hooksNode.next
    assert(hookNode.memoizedState, 'bar')
    hookNode = hooksNode.next
    assert(hookNode.memoizedState, 'baz')
  })

  return (
    <ChildComponent ref={childFiberRef} />
  )
}
```

* When using `useState`, can actually provide an action function that receives old state and returns the new one
  * `setX(oldState => operationToGetNewState(oldState))`
* Effect hooks are created during render time but run after paint
  * They're called in their definition order
* Effects are stored on the fiber in a proeprty called `updateQueue`
  * `create` property is a callback that's run after paint
  * `destroy` is the callback returned from `create()` that is run before the next initial render
  * `inputs` are a set of values that determine whether the effect should be destroyed and recreated
  * `next` is ference to next effect