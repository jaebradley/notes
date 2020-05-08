# [Learn CSS Grid In 5 Minutes](https://medium.com/free-code-camp/learn-css-grid-in-5-minutes-f582e87b1228)

* Need to have a wrapper and items for CSS grid
  * Wrapper is the grid container and items are the content inside the grid

```html
<div class="wrapper">
  <div>1</div>
  <div>2</div>
  <div>3</div>
  <div>4</div>
  <div>5</div>
  <div>6</div>
</div>
```

```css
.wrapper {
    display: grid;
}
```

* By itself, it will stack 6 `div`s on top of each other
* Add the `grid-template-columns` and  `grid-template-rows` properties
  * the values for the `grid-template-columns` will determine the width of the columns
  * the values for the `grid-template-rows` will determine the height of the columns
  * the number of values in both will determine the number of rows and the number of columns

```css
# 3 columns of 100px width each
# 2 rows of 50px height each
.wrapper {
    display: grid;
    grid-template-columns: 100px 100px 100px;
    grid-template-rows: 50px 50px;
}
```

* By defining `grid-column-start` and `grid-column-end`, that determines which grid lines an item starts and ends at
  * The number of column lines will be one more than the number of columns - so a grid with three columns will have four grid lines

```css
# We want item1 to start on the first grid line and end on the fourth column line
.item1 {
    grid-column-start: 1;
    grid-column-end: 4;
}
```