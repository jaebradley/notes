# [How To Understand the Keyword `this` and Context in JavaScript](https://medium.freecodecamp.org/how-to-understand-the-keyword-this-and-context-in-javascript-cd624c6b74b8)

* Context only makes sense inside functions
* `this` represents where you are, what object called the function
* `setTimeout` (and similar functions) are in global context
* `info` is a function inside `coffee` object, thus `this` is in context of object
  * Can also refer to `info` via `coffee.info`

```javascript
const coffee = {
  strong: true,
  info: function() {
    console.log(`The coffee is ${this.strong ? '' : 'not '}strong`);
  },
}

coffee.info(); // The coffee is strong
```

* When function is called in `setTimeout` context is global so `this` refers to global object
  * `this.amount` is actually `undefined` because `global` object does not have an `amount` property
  * To point to the proper context, need to `bind` function

```javascript
const coffee = {
  strong: true,
  amount: 120,
  drink: function() {
    setTimeout(function() {
      if (this.amount) {
        this.amount -= 10;
      }
    }, 10);
  }
}
```

* Imagine this `React` fragment with an event handler that modifies `state` using `this.setState`
* When this event handler gets called, there will be an error because the context where `React` calls the event handler is no longer in the context of the component
  * In this context, there is no `state` property on the context object (i.e. `this.state` does not exist)
  * Need to bind the event handler to `this` to the context of the component either in `props` or in the `constructor`

```jsx
someEventHandler() {
  this.setState({ foo: 'bar' });
}

render() {
  return (
    <div onClick={this.someEventHandler}>
      Test
    </div>
  )
}
```
