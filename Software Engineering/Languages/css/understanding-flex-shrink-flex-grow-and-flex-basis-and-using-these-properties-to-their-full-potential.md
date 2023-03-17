# [Understanding Flex Shrink, Flex Grow, And Flex Basis, And Using These Properties To Their Full Potential](https://medium.com/@tiffnogueira/understanding-flex-shrink-flex-grow-and-flex-basis-and-using-these-properties-to-their-full-e4b4afd2c930)

## `flex-grow`

* A `flex-grow` >= `1` means that we are telling element to grow to take up the available space
* A `flex-grow` set to `0` will not grow to take up available space
* If there are multiple elements with a `flex-grow` value, their values are summed together to see how much each individual element would grow if there was available space
  * So if there are two elements and one has a `flex-grow` value of `2` and the other has a `flex-grow` value of `1`, then the first element would get `2/3` of any available space and the second element would get `1/3` of any available space


## `flex-shrink`

* An element with a `flex-shrink` value of `0` will not shrink as page gets smaller (only true if there's no `flex-grow` value on the element)
  * If there is a `flex-grow` value, the element won't shrink smaller than it's content
* An element with a `flex-shrink` of `1` will shrink ,but not smaller than it's content
* If multiple elements have a `flex-shrink` they will be shrunk similar to how `flex-grow` "sums up" element values

## Examples with `flex-grow`, `flex-shrink` and `flex-basis`

* `flex: 1 0 200px;`
  * `flex-grow` of `1`, `flex-shrink` of `0` and `flex-basis` of `200px`
  * This element will be at minimum `200px` wide and will be allowed to grow if there is extra space - in this case, `flex-basis` is like a `min-width`
* `flex: 0 1 200px;`
  * `flex-grow` of `0`, `flex-shrink` of `1` and `flex-basis` of `200px`
  * `200px` at it's largest, but allowed to be smaller (`flex-basis` acting as `max-width`)
* `flex: 0 0 200px`
  * Will always be `200px` - same as just setting `width`
* `flex: 1 0 auto;`
  * `auto` tells element to be size of content
* `flex: 0 1 auto;` is default value the `flex` property is given