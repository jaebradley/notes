# [How to use CSS animations like a pro](https://stories.jotform.com/how-to-use-css-animations-like-a-pro-dfacc1e97338)

* CSS-only animations work much better compared to JavaScript-based DOM manipulation animations
* Use keyframes to control how animated object behaves at various stages of the animation

```css
@keyframes pulse {
  0% {
    // Some css
  }
  50% {
    // Some other css
  }
  100% {
    // Some other other css
  }
}
```

* Animation name - `pulse`
* 3 stages - `0%`, `50%`, `100%`
* Can control things like animation duration, animation delay, repetition, and direction using `animation*` CSS property
  * Can use `animation` property directly or specific fields like `animation-duration` / `animation-name`
* Animation timing determines how the animation will accelerate or decelerate through the movement cycle
* Animation direction
  * Normal - animation advances from `0%` to `100%`
  * Reverse - animation advances from `100%` to `0%`
  * Alternate - animation advances from `0%` to `100%` and then `100%` to `0%`
  * Alternate-reverse - animation advances from `100%` to `0%` and then from `0%` to `100%`
* `will-change` property can be used to inform browsers about elements that may apply animations
  * This allows browsers to potentially optimize resources to animate particular objects
