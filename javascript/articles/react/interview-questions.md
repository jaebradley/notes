# [React Interview](https://github.com/Pau1fitz/react-interview)

### Lifecycle methods

* `componentWillMount` - most commonly used for App configuration in your root component
* `componentDidMount` - all setup couldn't do without DOM and start getting all needed data (setting up event listeners)
* `shouldComponentUpdate` - allows finer-grained control over whether a component should render
* `componentDidUpdate` - used to update DOM in response to prop or state changes
* `componentWillUnmount` - here you can cancel any outgoing network requests, or remove event listeners

### Making AJAX request

* `componentDidMount` is where AJAX request should be made (only executed once during component's life)
* Executed when component "mounts" (is added to the DOM) for the first time

### Controlled component

* The component containing the form will keep track of the value of the input in it's state and re-render the component each time the callback function e.g. `onChange` is fired as the state updates

### HOC

* Function that takes a component and returns a new component
* `Redux`'s `connect` method
* Good way of sharing behavior between components

### Use `setState` callback function

* `this.props` and `this.state` are updated asynchronously, should not rely on their values when calculating next state

> For example lets consider at time t=0. Two setState functions were called in your application from different points in your application.Before t=0 they were referring same sate.As fired simultaneously they will modify this state only.Suppose one of them finished executing at t=3 thus modified state and created a new state(call as it state_1).Now second setState finished at t=4.It modified in initial State and created new state(call it as state_2).This new state(state_2) is different from state_1.And thus we can have a data discrepancy.
> Thus whenever your new state is dependent on previous State use state call back method. prevState always refer to state that is result of last modified in memory. Thus a call back function utilizing prevState never cause discrepancy in data.

 - [StackOverflow](https://stackoverflow.com/questions/42038590/when-to-use-react-setstate-callback)

```javascript
// may produce data discrepancies
this.setState(
  {counter:this.state.counter+1}
);
```

```javascript
// callback always refers to latest counter value
this.setState(
    (prevState,props)=>{
        return {counter:prevState.counter+1};
    }
);
```

### `super(props)`

* child class constructor cannot make of `this` until `super()` is called
* `ES2015` class constructors have to call `super()` if they are subclasses
* pass `props` to `super()` to enable access to `this.props` in the constructor

### `React.createElement` equivalent

```javascript
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```javascript
const element = React.createElement = (
  'h1',
  {className: 'greeting'},
  'Hello, world!',
);
```
