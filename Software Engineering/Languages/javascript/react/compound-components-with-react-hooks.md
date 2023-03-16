# [React Hooks: Compound Components](https://kentcdodds.com/blog/compound-components-with-react-hooks)

* In some cases, there are two or more commponents that work together to accomplish a useful task (`select` + `option`s)
  * Typically, one component is the parent and the other is the child
* In the case of `select` + `option`s, the API is expressive and flexible vs. something like `<select options="key1:value1;key2:value2" />`
* Additionally, the `select` contains implied state about the selected option and shares that state with its children so they know how to render themselves based on that state

## Toggle Example

```jsx
<Toggle>
  <Toggle.On>It is on</Toggle.On>
  <Toggle.Off>It is off</Toggle.Off>
  <Toggle.Button />
</Toggle>
```

* The `On`, `Off` and `Button` components are static properties on the `Toggle` component, which helps explicitly communicate the relationship
* Create a `ToggleContext` which stores the state of the `Toggle` component
* The `On` and `Off` components either show their `children` (or do not show their `children`) depending on the state of the `ToggleContext`
* The `Button` component will simply toggle the current state from `on` to `off` (and vice-versa)
