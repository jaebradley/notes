# [How To Test Custom React Hooks](https://kentcdodds.com/blog/how-to-test-custom-react-hooks)

* Don't mock built-in React hooks like `useState` and `useEffect` - throw away a lot of confidence doing such a thing
* Instead, create a dummy `TestComponent` and use that to determine value hook returns in various scenarios

```jsx
function setup(...args) {
  const returnVal = {};
  function TestComponent() {
    Object.assign(returnVal, useUndo(...args));
    return null;
  }
  render(<TestComponent />);
  return returnVal;
}

test('allows you to undo and redo', () => {
  const undoData = setup('one')
  // assert initial state
  expect(undoData.canUndo).toBe(false)
  expect(undoData.canRedo).toBe(false)
  expect(undoData.past).toEqual([])
  expect(undoData.present).toEqual('one')
  expect(undoData.future).toEqual([])
  // add second value
  act(() => {
    undoData.set('two')
  })
  // assert new state
  expect(undoData.canUndo).toBe(true)
  expect(undoData.canRedo).toBe(false)
  expect(undoData.past).toEqual(['one'])
  expect(undoData.present).toEqual('two')
  expect(undoData.future).toEqual([])
  // add third value
  act(() => {
    undoData.set('three')
  })
  // assert new state
  expect(undoData.canUndo).toBe(true)
  expect(undoData.canRedo).toBe(false)
  expect(undoData.past).toEqual(['one', 'two'])
  expect(undoData.present).toEqual('three')
  expect(undoData.future).toEqual([])
});
```

* Using `@testing-library/react-hooks` it does something very similar to `setup`
  * It also allows component to "rerender" allowing effect dependency changes
  * Also includes utility to "unmount" component to test effect cleanup functions

```jsx
test('allows you to undo and redo', () => {
  const {result} = renderHook(() => useUndo('one'))
  // assert initial state
  expect(result.current.canUndo).toBe(false)
  expect(result.current.canRedo).toBe(false)
  expect(result.current.past).toEqual([])
  expect(result.current.present).toEqual('one')
  expect(result.current.future).toEqual([])
  // add second value
  act(() => {
    result.current.set('two')
  })
  // assert new state
  expect(result.current.canUndo).toBe(true)
  expect(result.current.canRedo).toBe(false)
  expect(result.current.past).toEqual(['one'])
  expect(result.current.present).toEqual('two')
  expect(result.current.future).toEqual([])
  // add third value
  act(() => {
    result.current.set('three')
  })
  // assert new state
  expect(result.current.canUndo).toBe(true)
  expect(result.current.canRedo).toBe(false)
  expect(result.current.past).toEqual(['one', 'two'])
  expect(result.current.present).toEqual('three')
  expect(result.current.future).toEqual([])
});
```
