# [How To Create Horizontal Scrolling Containers](https://codeburst.io/how-to-create-horizontal-scrolling-containers-d8069651e9c6)

* HTML is a `div` with many `div` children that represent the "cards" in the container
* The `white-space` method uses the `white-space: nowrap` css property to disable wrapping of the `div`s
  * Combine this with turning off vertical scrolling (`overflow-y: hidden`) and enabling horizontal scrolling (`overflow-x: scroll`)
  * Set the `display` for each card to `inline-block` to display in a single row
  * This method does not work for IE or Edge
* The `flexbox` method sets the `flex-wrap` property of the `flex`ed parent container to `nowrap`
  * Each card's `flex` property is now `0 0 auto`
  * Solution works with IE and Edge (just need some vendor prefixes)
* Web pages on iOS scroll with momentum - for horizontal elements, that smooth scrolling won't exist by default
  * Need to add the `webkit-overflow-scrolling: touch` property to have similar scrolling behavior on horizontal elements
* To eliminate scrollbars (on webkit browsers) set the `-webkit-scrollbar` to `display: none`
