# [Difference Between CSS Position Absolute Versus Relative](https://medium.com/@leannezhang/difference-between-css-position-absolute-versus-relative-35f064384c6)

* By default, elements have a `position` value of `static`, which just positions an element based on its current position in the flow
  * The `top`, `right`, `bottom`, `left`, and `z-index` properties do not apply
* `position: relative` positions an element based on its current position without changing layout
* `position: absolute` positions an element based on its parent

```html
<div class=”parent”>
 <div class=”box” id=”one”>One</div>
 <div class=”box” id=”two”>Two</div>
 <div class=”box” id=”three”>Three</div>
 <div class=”box” id=”four”>Four</div>
</div>
```

```css
.parent {
  border: 2px black dotted;
  display: inline-block;
}

.box {
  display: inline-block;
  background: red;
  width: 100px;
  height: 100px;
}

```

* The above HTML results in 4 boxes that are horizontally aligned
* Giving `#two` `top: 20px` and `left: 20px` and `position: relative` will now offset it from it's previous position by the `px` values but the other 3 boxes' layout won't have changed - they will be in the same locations as in the default state
* Switching the previous `position: relative` to `position: absolute` will now move `#two` to position based on it's parent (which is the `.parent` `div`
  * So it will offset by the `px` value from the "start" of the container `div` which means that it will overlay on top of `#one`
  * The other `div`s in the group will change their layout and fill the space "vacated" by `#two`
  * So now `#one`, `#three`, and `#four` are all horizontally-aligned
