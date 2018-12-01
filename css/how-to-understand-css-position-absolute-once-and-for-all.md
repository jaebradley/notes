# [How To Understand CSS Position Absolute Once And For All](https://medium.freecodecamp.org/how-to-understand-css-position-absolute-once-and-for-all-b71ca10cd3fd)

* Position absolute is more about the element's container
* Element has to know parent container's position relative to it's own
  * Element has to know x and y positions set by top, bottom, left, and right
  * Which parent it's going to position itself relative to
* Set up is four nested `div`s
  * `div`s 1-3 are `display: flex`, `margin: auto`
  * These `div`s are nested and centered inside each other
* When fourth (i.e. inner-most `div`) has `position: absolute`, the `div` is removed from normal document flow
  * Without defined coordinates, it stays at default position which is parent's `div`s upper left corner
  * If inner-most `div` has `top: 0`, `left: 0` the element has to know which parent to consider reference
    * In order for an element to be considered a reference, it must be `position: relative`
    * It travels up through all of it's ancestors to ask if they are `position: relative` until it gets to tbe document body
    * If first `div` is `position: relative` then the element will be positioned relative to it
