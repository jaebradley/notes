# [Reconciliation](https://reactjs.org/docs/reconciliation.html)

* `render()` function creates a tree of `React` elements
* state of the art algorithms have a complexity in the order of `O(n^3)` where `n` is the number of elements in the tree
* React implements an `O(n)` algorithm with two assumptions
  * Two elements of different types will produce different trees
  * The developer can hint at which child elements may be stable across different renders with a `key` prop
* Whenever root elements have different types, React will tear down the old tree and build the new tree from scratch
* When comparing two React DOM elements of the same type, React keeps the same DOM node and only updates the changed attributes
* Same with `style` - React only updates the properties that have changed
* On prop update
  * React calls `componentWillReceiveProps()` and `componentWillUpdate()`
  * `render()` is called and the diffing algorithm recurses on the previous result and the new result


* React will match the first two elements in the list and then insert the third element
```html
/* before */
<ul>
  <li>first</li>
  <li>second</li>
</ul>

/* after */
<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
```
  * Performance is worse if the third element were to be inserted at the beginning of the list as React will mutate every child

* React uses the keys attribute to allow performant element insertion
  * the key value is used to match children in the original tree with children in the subsequent tree
