# [Unit Testing Custom React Hooks](https://medium.com/@nitinpatel_20236/unit-testing-custom-react-hooks-caa86f58510)

* Hooks cannot be called from anywhere like regular JS functions - can only be called from functional components or hooks
* So to test, need to create a dummy component from which to call hooks

```jsx
function TestHook({ callback }) {
  callback();
  return null;
}

export const testHook = (callback) => mount(<TestBook callback={callback} />);
```

* Pattern used by `react-testing-library`
* In test, use closure to wrap custom hook so that variable has access to output of the custom hook

```javascript
let nameField;
beforeEach(() => {
  testHook(() => {
    nameField = useTextField('name');
  });
});
```

* Then can test state updates by doing something like `nameField.onChange` and assert that the `nameField` value changes as expected
  * Need to wrap the `onChange` in `act` so `act(() => nameField.onChange())`
* For testing `useEffect`, mock the side-effect and then assert that the side-effect is called when expected
