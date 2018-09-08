# [React Gotchas](https://github.com/timhwang21/react-gotchas)

## In `mapStateToProps`, `props` should be pure

* `mapStateToProps` is called on *every* store change
* If assigned `props` are not pure, then action updates could trigger unnecessary re-renders

```javascript
// filter creates a new array
// this will (probably) cause a rerender on every action dispatch for the connected component

const mapStateToProps = (state, props) => ({
  activeFoos: props.foos.filter(foo => foo.isActive),
});

// The same is true for default values

const mapStateToProps = (state, props) => ({
  activeFoos: props.foos || [],
});
```

## `Reselect` selectors should be pure

* If selectors are not pure (e.g. a new array is created, then triple equality will fail)
  * This is important because if the selector is used in a `mapStateToProps` then the `props` will have changed every time `mapStateToProps` is invoked
  * Reselect selectors have a cache depth of `1` by default - if a non-pure selector is used, there will be a cache miss every time

## Be wary of `Reselect` selectors that use `props`

* Composing selectors that use `props` can create a less-than-clear dependency chain

```javascript
const getFlight = (state, props) => state.entities.flights[props.flightId];
const getLineItem = (state, props) => state.entities.lineItems[props.lineItemId];
const getTactic = (state, props) => state.entities.tactics[props.tacticId];

// The caller of getStuff now has to have the flightId, lineItemId, and tacticId props
const getStuff = createSelector(
  [getFlight, getLineItem, getTactic],
  (flight, lineItem, tactic) => ({ flight, lineItem, tactic })
);
```

## `Component` vs. `PureComponent`

* `Component` renders **faster** when `props` change often and **slower** when they don't
* `PureComponent` render **slower** when `props` change often and **faster** whey they do

## Add `displayName` to functional components

* If declared using fat arrows syntax, functional components do not have [a display name](https://reactjs.org/docs/react-component.html#displayname) by default
* This impacts debugging in two ways
  * In `ReactDevTools`, the functional component is not searchable by name
  * In `ReactPerfTools`, these functional components get lumped together
* Can be avoided using the `function()` syntax

## Avoid anonymous functions `props`

* It causes the component to always rerender since an anonymous function has a new reference and is considered a "new" prop

## Avoid "naked" arrays and objects in `props`

* These too will have new references and will not be considered equal

```jsx
render() {
  return (
    <Foo
      a={[]}
      b={{}}
    />
  )
}
```

## Instead of creating arrow functions over a list of components, use a wrapper

* This will cause `Checkbox`es to re-render every single time

```jsx
// In SomeComponent.jsx

render() {
  return (
    <div>
      this.props.values.map(value => <Checkbox onClick={() => this.handleClick(value)} />)
    </div>
  );
}
```

* Instead, create a `WrapperCheckbox` component

```jsx
// WrapperCheckbox.jsx

// Assume this gets bound in the constructor
handleClick() {
  const {
    value,
    onClick,
  } = this.props;

  onClick(value);
}

render() {
  return <Checkbox onClick={this.handleClick}
}
```
