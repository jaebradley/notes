# [CSS Units For `font-size`: `px` | `em` | `rem`](https://medium.com/code-better/css-units-for-font-size-px-em-rem-79f7e592bb97)

* If a user changes their default font size, pixels for spacing and layout won't change
* `em` is equal to the computed font-size of an element's parent
  * A `div` with `font-size: 16px` means that for the `div` and all its children, `1em = 16px`
* It is a good practice not to define `font-size` explicitly except for root elements when using `em`
* `rem`s are relative to the root `html` element and not to a parent element
  * If the root element does not explicitly define a `font-size` then `1rem` will be equal to the default `font-size` provided by the browser (usually `16px`)
* If `font-size` is set to `62.5%` then `1rem = 10px` which can sometimes make calculations easier
