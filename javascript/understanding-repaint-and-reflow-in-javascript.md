# [Understanding Repaint and Reflow in JavaScript](https://medium.com/@suhas010/what-the-heck-is-repaint-and-reflow-in-the-browser-b2d0fb980c08)

## Render Tree

* Render tree is like a DOM tree but doesn't match it exactly
* Render tree knows about styles - so a `div` with `display: none` won't be represented in the render tree
  * Same for `head`
* Render tree might contain many nodes for a given DOM element
  * Each line in a `p` needs it's own render node
* A node in a render tree is called a frame and each node has CSS box properties like width, height, etc.
* Once render tree is created, the browser can paint the render tree nodes to the screen

```html
<p>
  <strong>line 1</strong>
  <strong><b>line 2</b></strong>
</p>
```

* DOM Tree might look like
  * p
    * strong
      * text node
  * p
    * strong
      * text node
* While render tree might look like
  * p
    * line 1
    * line 2

## Repaint and Reflow

* Repaint is nothing but changing the visibility of an element without affecting layout (like changing outline or background)
* Reflow means re-calculating the positiosn and geometries of elements in the document for the purpose of re-rendering part (or all) of the document
  * Reflow blocks the user from interaction with the browser
