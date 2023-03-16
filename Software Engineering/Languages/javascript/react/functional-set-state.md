# [Functional `setState`](https://medium.freecodecamp.org/functional-setstate-is-the-future-of-react-374f30401b6b)

## Review

* `React` is a component-based UI library - a component is basically a function that accepts props and returns a DOM element
* Most ways of managing state in `React` involve passing the `setState` method an object

```javascript
class Foo extends Component {
  ...
  incrementScore() {
    this.setState({ score: this.state.score + 1 });
  }
  ...
}
```

## Functional `setState`

* In addition to an object, `this.setState` accepts a function
  * This function has two arguments (*previous state*, and *current props*)
  * So to replicate our previous object-based state change...

```javascript
incrementScore() {
  this.setState(function(previousState, currentProps) {
    return { score: previousState.score + 1}
  });
}
```

## Asynchronous state updates

* Here's what happens when `setState` is called with an object
  * `React` merges the object into the current state
  * Then ["reconciliation"](https://reactjs.org/docs/reconciliation.html) starts
    * `React` creates a new `React Element` tree
    * Diffs the old tree against the new one
    * Identifies changes related to state changes
    * Updates the DOM
* Because this set of operations is non-trivial, `React` may batch multiple `setState` calls into a single update for performance reasons
* When `React` batches state changes, it extracts all objects passed to `setState`, merges them together into a single object, and then uses that single object to update the DOM
* Because Javascript merging implies that for a set of keys, the last object that is merged with that key, "wins"

```javascript
const me = { name: 'me' },
      you = { name: 'you' },
      us = Object.assign({}, me, you);

us.name === 'you'; // true
console.log(us); // { name: 'you' }
```

* __TL;DR Because `this.props` and `this.state` may not be updated asynchronously, you should not rely on their values for calculating the next state__

## So why does functional `setState` work?

* Updates will be queued and later executed in the order they were called
* That means a state changing function like

```javascript
increaseScoreBy3() {
  this.setState( (state) => ({ score: state.score + 1 }));
  this.setState( (state) => ({ score: state.score + 1 }));
  this.setState( (state) => ({ score: state.score + 1 }));
}
```

looks like

```javascript
const updateQueue = [
  (state) => ({ score: state.score + 1 }),
  (state) => ({ score: state.score + 1 }),
  (state) => ({ score: state.score + 1 })
];
```

* This implies that you can move state changing methods outside your component class for maximum reusability
* Your component class no longer cares how the state updates, it just declares the type of update it would like to execute
* This also makes testing a lot easier, as you can test the state-changing logic outside the scope of any containing class
* You can also wrap the state-changing function in another function to pass extra arguments

```javascript
function multiplyBy(multiplier) {
  return function update(state) {
    return {
      value: state.value * multiplier,
    };
  };
}

class Foo extends Component {
  ...
  handleMultiplyByFive = () => {
    this.setState(multipleBy(5));
  }
  ...
}
```

## Code Pens
https://codepen.io/mrscobbler/pen/JEoEgN
http://jsbin.com/najewe/edit?js,console
