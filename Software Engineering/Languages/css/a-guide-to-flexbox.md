# [A Guide To Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

* Give container ability to alter items width / height to best fill available spaces (accommodating display devices / screen sizes)
* Direction agnostic
  * Regular layouts use block to control vertical and inline to control horizontal

## Container

* `display: flex`
  * Enables flex content on all *direct* children
  * CSS columns have no effect on flex container
* `flex-direction`
  * establishes main axis i.e the direction which items are placed
  * `row`, `column` (also `row-reverse` and `column-reverse`)
* `flex-wrap`
  * by default items will try to fit on one line
  * can specify `wrap` and `wrap-reverse` to wrap multiple lines
* `justify-content`
  * distribution of items along main axis
  * can define distribution of extra space
  * `center` - items centered along main axis
  * `space-between` - items are evenly distribution along main axis
    * first item is on start line, last item is on end line
  * `space-around` - items have equal spacing
    * This means that non-end items have "double" spacing since the right spacing of the left neighbor combines with the left spacing of the item
  * `space-evenly` - items have equal spacing *between* two items
* `align-items` - how items are aligned with respect to the main axis
  * for example, `flex-start` would align items starting from the "top" of the container
* `align-content` - `justify-content` but for the different lines in a container

## Children

* `flex-grow` - ability for an item to grow with respect to other items
  * If all items have a `flex-grow` of `1` the container will be distributed equally to all children
  * If one of the children has a value of `2`, it's space will take up twice as much space as the remaining children
* `align-self` - overrides `align-items` value
