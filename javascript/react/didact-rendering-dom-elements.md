# [Didact: Rendering DOM Elements](https://engineering.hexacta.com/didact-rendering-dom-elements-91c9aa08323b)

* Use POJO to describe what and how to render
  * POJOs have a `type` and `props`
  * `type` can be function or string
  * `props`is an object
    * `props` can have a `children` property, which should be an array of Didact elements
* These are equivalent

```javascript
{
  type: 'div',
  props: {
    children: [
      {
        type: 'input',
        props: {
          value: 'foo',
          type: 'text',
        }
      }
    ]
  }
}
```

```html
<div>
  <input value="foo" type="text">
</div>
```

* `render` function takes a Didact element (i.e a POJO defined above) and a parent element for context
  * The function creates a new DOM element using the information defined by the Didact element and appends the new DOM element to the parent
  * The function will also create any nested / child DOM elements for the Didact elements defined in the `children` props
    * This will involve calling `render` recursively (for the child elements)
  * While elements are rendered on DOM, will still need to handle event handlers and other `prop` values
    * Can take advantage of `addEventListener` after identifying `props` that are event handlers
    * For all remaining `props`, can just apply `prop` to DOM element object (`el[propName] = props[propName]`)
  * Custom handle text elements (`<span>Some Text</span>`)
    * `Some Text` actually is `{ type: 'TEXT_ELEMENT', props: { nodeValue: 'SOME TEXT' } }`
