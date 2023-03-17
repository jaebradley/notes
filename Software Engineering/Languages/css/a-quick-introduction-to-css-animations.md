# [A Quick Introduction to CSS Animations](https://medium.com/free-code-camp/a-quick-introduction-to-css-animations-a1655375ec90)

```css
.container {
  animation: spin 10s linear infinite;
}
```

* Applies an animation named `spin` with a duration of 10 seconds, without any acceleration or difference in speed (i.e. "linear") and to repeat infinitely

```css
@keyframes spin {
  0% {
    transform: rotateZ(0);
  }
  100% {
    transform: rotateZ(360deg);
  }
}
```

* define the previously referenced animation (`spin`) using keyframes using `0%` and `100%` waypoints (though can have as many as you'd like)
* Rotate z-axis from 0 to 360 completing full loop
* Definition says nothing about timing - this is all defined when defining the `animation` property

## Animation properties

* `name` - references animation created using keyframes
* `duration` - how long animation should last, seconds
* `timing function` - linear or ease, for example - default is ease
* `delay` - optional number of seconds to wait before starting animation
* `iteration` - how many times animation should be performed - default is `1`
* `play state` - default is `running` but when set to `paused` obviously it will pause the animation
