# [Understanding use of the +, >, and ~ symbols in CSS selectors](https://levelup.gitconnected.com/understanding-use-of-the-and-symbols-in-css-selectors-95552eb436f5)

```css
<div>
  <p>Apple</p>
  <div>
    <p>An apple a day keeps the doctor away!</p>
  </div>
  <p>Banana</p>
  <p>Cherry</p>
</div>
```

* The space is the descendant selector
  * `div.container p` will target all `p` tags within `#container`, at any depth
* The `?` symbol is the child selector - CSS rules will only be applied to elements which are the direct children of a particular element
* The `+` symbol is the adjacent sibling selector - it will select all elements that are the adjacent siblings of a specified element
  * Adjacent sibling means that the elements have the same parent element and the adjacent elements immediately follows
  * In the example CSS, this means that the styles would apply to only `Banana`
* The `~` symbol is the general sibling selector - it selects all elements that are siblings of a specified element
  * `div ~ p` would select all `p` elements that are siblings of `div` elements
  * In the example CSS this would be `Banana` and `Cherry`

