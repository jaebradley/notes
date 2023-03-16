# [How Does setState Know What to Do?](https://overreacted.io/how-does-setstate-know-what-to-do/)

* `React.Component` might contain DOM update logic
  * But if so, how does `setState` work in different environments
* Comomn misconception that the React "engine" lives inside `react` package
  * `react` package only exposes APIs for _defining_ components
  * Implementation of React lives in renderers (`react-dom`, `react-dom/server`, `react-native`, `react-test-renderer`, `react-art`)
* This is why `react` package is useful - whether running React DOM, React DOM Server, or React Native, components would import and use them in the same way
* This is also why both `react` and `react-dom` packages need to be updated for new features
* `React.createContext` (for example) doesn't implement context feature - it just returns plain JavaScript objects
  * It's the renderer (like React DOM) that implements how to handle `Context`s
* How does component know which renderer to talk to?
  * Every renderer sets a special field on the created class
  * This field is _updater_ - the renderer sets this after creating instance of class
  * `setState` delegates work to renderer (which is how it can update DOM even though defined in `react` package

```jsx
setState(partialState, callback) {
  this.updater.enqueueSetState(this, partialState, callback);
}
```

* Hooks use `dispatcher`
  * `React.useState`, `React.useEffect` all forward to current dispatcher
  * Renderers set dispatcher before rendering component (this overrides `React.__currentDispatcher`)

```jsx
// React DOM
const prevDispatcher = React.__currentDispatcher
React.__currentDispatcher = ReactDOMDispatcher;
let result;
try {
  result = YourComponent(props);
} finally {
  // Restore previous dispatcher back
  React.__currentDispatcher = prevDispatcher;
}
```

