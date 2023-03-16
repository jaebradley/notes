# [Z-Index Explained: How To Stack Elements Using CSS](https://medium.freecodecamp.org/z-index-explained-how-to-stack-elements-using-css-7c5aa0f179b3)

* Default stacking order (without `z-index`)
  * Root (`<html>`) element
  * Non-positioned elements in the order they are defined
  * Positioned elements in the order they are defined
* Non-positioned elements are elements with a default position value of static
* Positioned elements are elements with any other position value (like `absolute`, `relative`, or `fixed`)
* `z-index` can only work with positioned elements

## Stacking Context

* When an element has a `z-index` that is something other than `auto`, it forms a new stacking context
* `z-index` values are only compared within a stacking context so when a parent element creates a new stacking context, it's child elements compare `z-index` values in that stacking context and not the global stacking context
* Stacking contexts are also formed when using `filter`, `opacity` and `transform`
