# [Didact Element Creation and JSX](https://engineering.hexacta.com/didact-element-creation-and-jsx-d05171c55c56)

* Object representation of elements was very verbose

```javascript
// This can be condensed to...
const element = {
  type: 'div',
  props: {
    id: "container",
    children: [
      {
        type: "input",
        props: {
          value: "foo",
          type: "text",
        }
      }
    ]
  }
}

// ...this JSX
const element = (
  <div id="container">
    <input
      value="foo"
      type="text"
    />
  </div>
)
```

* JSX is not valid JavaScript so code needs to be transformed to valid JS via a preprocessor, like `babel`
* `babel` transforms the above JSX to

```javascript
const element = createElement(
  "div",
  { id: "container" },
  createElement(
    "input",
    { value: "foo", type: "text" }
  )
);
```

* First argument of `createElement` is the type of the element
* Second argument of `createElement` is the `props` for the element
* Third argument is each child in the element, wrapped in it's own `createElement`
* `createElement` needs to handle text elements, as they're passed as strings
  * Need to add `type` and `props` values - use a custom `TEXT_ELEMENT` constant type, and a `nodeValue` `prop` that's assigned the text value
