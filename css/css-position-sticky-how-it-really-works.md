# [CSS Position Sticky - How It Really Works!](https://medium.com/@elad/css-position-sticky-how-it-really-works-54cd01dc2d46_

* When an element is given `position: sticky` the sticky item's container is the only area in which the sticky item can stick
  * The item can only float over sibling elements
* The sticky container is the HTML element that wraps the sticky item and determines the maximum area that the sticky item can float in
* At the end of the sticking area, the sticky element stops and stacks on top of the other elements, like an absolutely-positioned element inside a `position: relative` container
