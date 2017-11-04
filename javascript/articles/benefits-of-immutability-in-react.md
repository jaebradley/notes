# Benefits of Immutability in React

## Object Equality (i.e. references)
> Let's say you have a regular JS array. There is a reference to it in memory and it has a certain value. Now you change an element in that array. You've just mutated it. In JS, the reference in memory doesn't change, but now the value of what it's pointing to has. So in order to know if the value has changed, you need to do a comparison on every element on that array, which is expensive.\n
Now let's say you have a immutable array (such as from the Immutable FB library or Mori). If you change an element in that array, you get a new array and a new array reference in memory. In other words, if you check that the reference in memory is the same, you are guaranteed that the value is the same (in this case, the array). So checking for equality is fast and cheap because you are only checking the reference, not each value in the array.\n
One way this is important in React is that now if your component state (or entire app state) is represented by an immutable data structure, you can just check for reference equality in the shouldComponentUpdate() method when deciding to re-render that component. If the reference is equal, you are guaranteed that the data backing that component hasn't changed and you can return false, telling React not to re-render.

[Source](https://news.ycombinator.com/item?id=8108394)

## Explicit State Changes (in Code)
Immutable objects make state changes more explicit.

```javascript
const someState = { foo: 'foo', bar: 'bar' };

// here's our mutating function
const changeFooValueToBaz = function(value) {
  value.foo = 'baz';
}

changeFooValueToBaz(someState);

// someState should equal { foo: 'baz', bar: 'bar' };
```

This is not as explicit as

```javascript
const someState = { foo: 'foo', bar: 'bar' };

// here's our immutable function
const changeFooValueToBaz = function(value) {
  const updatedValue = Object.assign({}, value);
  updatedValue.foo = 'baz';
  return updatedValue;
}

const newState = changeFooValueToBaz(someState);
someState.foo   // foo
someState.bar   // bar
newState.foo    // baz
newState.bar    // bar
```

In my opinion, I think immutability presents some obvious benefits from a readability perspective.

[Source](https://www.bennadel.com/blog/2903-why-should-i-care-about-immutable-data-in-reactjs.htm)

## Reasoning About / Replaying / Debugging State Changes
* With an immutable approach, it's hard to change state in an unexpected way (i.e. you have to create and return a new object like in the example above). This is really useful for debugging large applications where various components can change state.
* I also think it aids in reasoning about the global state -- immutability is at the heart of Redux and Redux's flow of presentation change => triggers state change via actions => triggers creation of a new global state => passes down new global state to all subscribers is simple and easy to debug.
* Finally, if you ever have [to implement an undo or redo feature](https://medium.com/@sharifsbeat/the-power-of-immutability-and-react-daf46f2a5f4d), immutability makes the implementation process much easier than using a mutating implementation (which would probably use caches).
[Source](https://stackoverflow.com/questions/34385243/why-is-immutability-so-importantor-needed-in-javascript)

## Why Immutability Might Not Be Important
And because there's always two sides to a coin, I think this is an informative post about why immutability [__may not be as important as I think__](https://stackoverflow.com/a/43318963/5225575).
