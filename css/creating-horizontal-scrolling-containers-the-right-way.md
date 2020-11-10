# [Creating Horizontal Scrolling Containers The Right Way](https://uxdesign.cc/creating-horizontal-scrolling-containers-the-right-way-css-grid-c256f64fc585)

* The scroll wrapping element uses `display: grid` and sets `grid-template-columns` to `20px 1fr 20px` so that there are `20px` gutters on the ends
* Define a `full` class under the scroll wrapping element with `grid-column: 1/-1` where any element with the `full` class will span the entire viewport without any padding
* By adding `grid-template-columns: repeat(6, calc(50% - 40px))`, this defines how much space each card should take up
  * Each card takes up 50% of the viewprt, minus the `40px` gutters lengt
* This leads to cards being cut off at both ends, so add the `full` class to the scrolled content container and add `padding: 0 20px` to compensate fo the gutters
* However, this leads to the last card being cut off, so add two `10px` empty spaces to the `grid-columns` at either ends
  * `grid-template-columns: 10px repeat(6, calc(50% - 40px)), 10px`
  * In order to prevent the first card from taking up the space of the first column of `10px`, create two empty pseudo-elements

```css
.wrapper::before,
.wrapper::after {
  content: '';
}
```

* The pseudo-elements are added to the start and end of the container
* One downside of this approach is that it's not dynamic so if something other than 6 cards are needed, the CSS will need to be adjusted
* One way of handling this is to count the cards and then assign the number using a CSS variable
  * `wrapperElement.style.setProperty('--total', n)`
  * In `grid-template-columns`, `repeat(var(--total), calc(50% - 40px))`
