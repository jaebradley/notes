# [Web Performance: Minimising DOM Reflow / Layout Shift](https://medium.com/better-programming/web-performance-dom-reflow-76ac7c4d2d4f)

* DOM reflow happens when 
  * Elements are inserted / removed / updated in the DOM
  * Animating a DOM element
  * Measuring an element through `offsetHeight` or `getComputedStyle`
  * Changing a CSS style
  * Setting the `style` attribute
  * Changing the `class` of an element
  * Resizing the window
  * Scrolling
  * Changing the font
  * Activation of CSS-pseudo classes like `:hover`
* Reflow will also trigger reflows on elements around it if it changes their position / layout
* Reflow is a blocking operation
* By hiding elements using `visibility: hidden` vs. `display: none` the element will stick take up space in the DOM layout so the element's width / height won't change and the browser does not need to recalculate the layout
* Chrome DevTools has a tool that shows `Layout Shift Regions` and helps visualize all places in a website where layout shift is occurring
