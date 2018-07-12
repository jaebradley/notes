# [Learn CSS Flexbox in 5 Minutes](https://medium.freecodecamp.org/learn-css-flexbox-in-5-minutes-b941f0affc34)

* Two main components of Flexbox layout are `container` and `items`
* Base HTML is

```html
<nav class="container">
  <div>Home</div>
  <div>Search</div>
  <div>Logout</div>
</nav>
```

* Without Flexbox, the `div`s are stacked on top of each other
* By giving the `container` the CSS property `display: flex`, this will automatically position the items nicely along the horizontal axis

## Center items along the main axis

* Main axis is the x / horizontal axis while the cross axis is the y / vertical axis
* `display: flex` + `justify-content: center` will center items along the main axis
* `justify-content: space-between` will add space between the items

## Moving single items

* If we want first two items on the left and then `logout` on the right

```css
.logout {
  margin-left: auto;
}
```

## Responsiveness

* Stretches items to fill container

```css
.container > div {
  flex: 1;
}
```

* only `search` item has flexible width

```css
.search {
  flex: 1;
}
```
