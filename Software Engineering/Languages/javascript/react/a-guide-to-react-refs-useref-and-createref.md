# [A Guide To React Refs: useRef And createRef](https://blog.logrocket.com/a-guide-to-react-refs/)

* Ref used to obtain node reference for modification
  * Like focussing on an element programmatically by calling `focus()` on a node instance
* Ref rule is for when you need to imperatively call a function for a behavior React doesn't allow you to control
  * You need to call a function, and that function has no association with a React method or artifact
* An example of this anti-pattern is using an `input`'s `ref.current.value` to update state vs. using `setState`
* Passing a ref to a React component will get the component reference and not an underlying `input` element
  * Can use `React.forwardRef` which allows you to define internally what element the `ref` will point at
  * So `ref` will point to underlying `input` without having to expose internals

```jsx
const LabelledInput = (props, ref) => {
  const { id, label, value, onChange } = props

  return (
    <div class="labelled--input">
      <label for={id}>{label}</label>
      <input id={id} onChange={onChange} value={value} ref={ref} />
    </div>
  )
}

export default React.forwardRef(LabelledInput);
```