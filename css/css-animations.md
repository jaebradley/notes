# [CSS Animations](https://www.leejamesrobinson.com/blog/learning-css-animations-by-example/)

* CSS3 animations allow you to gradually change from one style to another
* Author tries to make a color-changing, spinning logo
* Use `@keyframes` rule to define those styles change at certain times

### `@keyframes` rule called `color` to change the fill selector color based on percentage complete
```css
@keyframes color {
  0% {
    fill: #99002f
  }
  50% {
    fill: #ffc426
  }
  100% {
    fill: #99002f
  }
}
```

* In order to bind this animation to an element, need to use `animation` property

### Applying animation to have logo change colors
```css
svg path {
  animation-name: color;
  animation-duration: 2s;
  animation-iteration-count: infinite;
  animation-timing-function: cubic-bezier(.85,.01,.25,1);
}
```

Note the `animation-name` selector

### Create a `@keyframes` rule for spinning
```css
@keyframes spin {
  0% {
    transform: rotateX(-20deg) rotateY(20deg);
  }
  100% {
    transform: rotateX(-20deg) rotateY(740deg);
  }
}
```

### Apply this `spin` rule to the container element
```css
.container {
  animation: spin 2s infinite cubic-bezier(.85,.01,.25,1);
}
```
