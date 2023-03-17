# [How Are Function Components Different From Classes?](https://overreacted.io/how-are-function-components-different-from-classes/)

```jsx
function ProfilePage(props) {
  const showMessage = () => {
    alert('Followed' + props.user);
  }

  const handleClick = () => {
    setTimeout(showMessage, 3000);
  }

  return (
    <button onClick={handleClick}>Follow</button>
  );
}
```

* Behavior is a button that simulates a network request with `setTimeout` which then shows a confirmation alert

```jsx
// Class translation (naive - i.e. it has a subtle difference)
class ProfilePage extends React.Component {
  showMessage = () => {
    alert('Followed ' + this.props.user);
  }

  handleClick = () => {
    setTimeout(this.showMessage, 3000);
  }

  render() {
    return <button onClick={this.handleClick}>Follow</button>;
  }
}
```

* The implication of `this.props.user` is that it'll read the `user` value from the latest `this.props` value whereas the functional component will read the `props.user` value from the render associated with the creation of that event handler
* UI is conceptually a function of current application state
* Event handlers are part of render result - just like visual output
  * Event handlers "belong" to particular render with particular props and state
  * Calling a callback reading from `this.props` means that `showMessage` callback is not tied to any particular render and so it loses correct `props` reference

```jsx
showMessage = (user) => {
  alert('Followed ' + user);
}

handleClick = () => {
  const { user } = this.props;
  setTimeout(() => this.showMessage(user), 3000);
}
```

* How to fix previous problem using closure
* However, doesn't fix long-term problem when `this.props.something` or `this.state.something` needed

```jsx
// Closure is established inside render
// This means that they should always stay exactly the same
render() {
  const props = this.props;
  const showMessage = () => {
    alert('Followed ' + props.user);
  }

  const handleClick = () => {
    setTimeout(showMessage, 3000);
  }

  return <button onClick={handleClick}>Follow</button>;
}
```

* Notice that the `render` is starting to look very similar to functional component definition

## Using refs to read current value

* In functional components, what if you _want_ to read the latest props or state that doesn't belong to particular render
  * Ref plays same role as instance field
  * Can call `current` property on ref to get current value

```jsx
const latestMessage = useRef('');
useEffect(() => {
  latestMessage.current = message;
});

const showMessage = () => {
  alert('You said: ' + latestMessage.current);
};
```

* Assignment inside effect so that ref value only changes after DOM has been updated
