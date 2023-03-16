# [Website Design: Creating the Layout in HTML](https://medium.com/elfsight-blog/website-design-creating-the-layout-in-html-887c6b30cf13)

* Websites designed using `table` are fixed and must be redesigned / rewritten when handling new changes
  * `table` is only supposed to display tabular data, so don't use for website layout (especially when keeping responsiveness in mind)
* Can use `float` and `div`s to create n-column layouts
 
 ```html
<html>
  <head>
  </head>
  <body>
    <div class="header"></div>
    <div class="topmenu"></div>
    <div class="content">
      <div class="sidebar"></div>
      <div class="mainbar"></div>
    </div>
    <div class="footer"></div>
  </body>
</html>
 ```

 ```css
/* Create two columns that floats next to each other */
.sidebar {
    float: left;
    width: 30%;
}

.mainbar {
    float: left;
    width: 70%;
}

/* Clear floats after the columns */
.content:after {
    content: "";
    display: block;
    clear: both;
}
 ```

 * `float` column should always be cleared or the layout of elements that follow will be messed up
  * `after` creates a pseudo-element that is last child of `.content` that clears `float`s
* To help develop responsive web design, add `<meta name="viewport" content="width=device-width, initial-scale=1.0">
` to web page
  * Add media query to add breakpoint at various device sizes (like `800px` for tablets and smartphones)
* CSS Grid is superior to flex because it operates in two dimensions whereas flex only operates in a single dimension

```css
.some-grid-container {
  display: grid;
  // Three columns with equal row and column spacing
  grid-template-columns: auto auto auto;
}
```

```css
.item1 { grid-area: header; }
.item2 { grid-area: menu; }
.item3 { grid-area: main; }
.item4 { grid-area: right; }
.item5 { grid-area: footer; }

.grid-container {
  display: grid;
  grid-template-areas:
    'header header header header header header'
    'menu main main main right right'
    'menu footer footer footer footer footer';
  grid-gap: 10px;
}

.grid-container > div {
  text-align: center;
}

----    Header    ----
Menu -- Main -- Right 
 |  --- Footer  ------
```

* Looks like it builds 6 columns and within each row definition you can define what each cell / column content type should be (like `header` or `menu` or `main`)
