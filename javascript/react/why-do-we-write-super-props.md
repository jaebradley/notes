# [Why Do We Write super(props)?](https://overreacted.io/why-do-we-write-super-props/)

* `super` refers to the parent class (`React.Component`) constructor (when `super` is called in `constructor` of custom React component)
* You can't use `this` in a constructor until _after_ you've called the parent constructor
* Passing `props` down through `super` initializes `this.props`, but `super` without `props` will also allow `this.props` being accessed in `render`
* React assigns `props` on the instance right after calling constructor

```jsx
const instance = new YourComponent(props);
instance.props = props;
```

* This was because when React added support for classes, the goal was to support as wide array of class abstractions as possible like `CoffeeScript`, `ES6`, and `TypeScript`
  * So React was intentionally unopinionated about whether calling `super` is required
* Always pass down `super(props)` even though it's not strictly necessary
