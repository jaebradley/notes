# [Common Responsive Layouts With CSS Grid (And Some Without!)](https://medium.com/samsung-internet-dev/common-responsive-layouts-with-css-grid-and-some-without-245a862f48df)

* Responsive images in a grid
  * `grid-template-columns: repeat(auto-fill, minmax(250px, 1fr))`
    * `repeat` takes two args - first defines number of column tracks, and the second, the width of the tracks
    * `auto-fill` will create a grid with as many tracks as will fit into the container that are a minimum of `250px` and a maximum of `1fr`
    * The width of the elements in the row will increase from `250px` until the point where another element could potentially fit beside the first
  * Using `object-fit: cover` will stop image from stretching but can cause cropping
* Full page image gallery - list of photos on small screen but expands to a full gallery with different image sizes for various images to make certain images stretch over two or more slots in the track
  * `grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr;` creates a grid with 6 columns which are each fraction unit wide so that each column shares `1/6` of the width of the container (could also write it as `grid-template-columns: repeat(6, 1fr)`)
* Holy Grail layout is a page with a header and footer that stick at the top and bottom of the window respectively and a content section that is split into two sidebars and the main content sits between them

```css
body {
    display: grid;
    grid-template-columns: 200px 1fr 200px;
    grid-template-rows: auto 1fr auto;
    height: 100vh;
}
```

* CSS creates a 3 by 3 grid with fixed width (`200px`) columns on either side 
* Setting the header and footer to `grid-column: span 3;` will make them take up an entire row each
* Use media queries to use flexbox and `flex-direction: column` at small screen sizes
* The main content element can be set to `flex: 1;` which will cause it to stretch to fill height of window
  * It also means that it doesn't require any heights being set on internal elements meaning that they can contain content without causing problems to layout