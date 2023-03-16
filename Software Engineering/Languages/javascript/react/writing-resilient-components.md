# [Writing Resilient Components](https://overreacted.io/writing-resilient-components/)

* Go through lint rules enabled in project config and ask if rule has ever helped catch a bug
  * If not, turn rule off
* At very least, team should have process for removing rules that cause friction

## Memoize expensive calculations

* One reason people copy props to state is to compute calculations

```jsx
state = {
  textColor: expensiveCalculation(this.props.color);
};

render() {
  return <button className={'Button-text-' + this.state.textColor}>Some Button</button>;
}
```

* However, component doesn't recalculate on `props.color` change
  * Easiest fix is to move calculation to `render` and make component a `PureComponent`
* However, expensive calculation will occur whenever _any_ `props` change, which may not be related (so expensive calculation is unnecessary)

```jsx
componentDidUpdate(previousProps) {
  if (previousProps.color !== this.props.color) {
    this.setState({
      textColor: expensiveCalculation(this.props.color);
    });
  }
}
```

* Use `componentDidUpdate` to execute calculation only when `props.color` changes
  * This would trigger a second re-render

```jsx
const textColor = useMemo(
  () => expensiveCalculation(color),
  [color],
);
```

* Don't recalculate `textColor` until `color` changes

## Side Effects Need to Be Cognizant of Data Flow

```jsx
componentDidMount() {
  this.fetchResults();
}

fetchResults() {
  const url = this.getFetchUrl();
  // Do fetching
}

getFetchUrl() {
  return 'http://myapi/results?query' + this.props.query;
}
```

* `fetchResults` uses `query` prop for data fetching
  * If `query` prop changes, `fetchResults` won't update
* In `componentDidMount` note all `state` and `props` in methods
  * Make sure that when `props` change, the side-effect is re-run
  * Can get challenging to remember everything
* `useEffect` hook can make this more transparent by exposing `dependencies`

```jsx
useEffect(() => {
  function fetchResults() {
    // function definition
  }

  function getFetchUrl() {
    // uses query and currentPage
  }

  fetchResults();
}, [currentPage, query]);
```

## Ensure that `shouldComponentUpdate`s are actually accurate

* If a function / event handler is passed via `props` and _not_ included in a `shouldComponentUpdate` then if the event handler changes, the behavior at the component-level will not update
* Recommendation is to avoid manually implementing `shouldComponentUpdate` and a custom comparison to `React.memo`
* Instead simply use `React.memo(YourComponent)`, which uses shallow comparison (or `PureComponent`)
  * This will ensure that passing a different function as a prop will update

## No Component is a Singleton

* Each of these components might assume it's the only component on the page
* To test, render app twice - does app behave as expected or are there strange crashes and errors

```jsx
componentWillUnmount() {
  // Resets something in Redux store
  this.props.resetForm();
}
```

* If there are two components on the page, unmounting one component can break the other
  * Resetting global state on mount is no better
* Showing or hiding a UI tree shouldn't break components outside of that UI tree

## Keep Local State Isolated

* If you're not sure whether some state is local, ask yourself: "If this component was rendered twice, should this interaction reflect in the other copy?"
  * Whenever the answer is "no", you found some local state
* `Post` component example (has a list of `Comment` threads and a `NewComment` input
  * If same `Post` was rendered twice
  * Post content should be updated if one post was updated - therefore, content should not be local state but live in a cache like `Apollo`, `Relay` or `Redux`
  * List of comments should be updated if one post has a comment added to it
  * Expanded comments
    * Weird if expanding a comment in one tree would also expand comments in the other tree
    * `expanded` flag should be a local state on `Comment`
  * `NewComment` input - would be weird if typing a comment in one input would update input in other tree
