# [Gain Motion Superpowers with `requestAnimationFrame`](https://medium.com/@bdc/gain-motion-superpowers-with-requestanimationframe-ecc6d5b0d9a4)

* Method that takes a callback that will be executed on the next repaint
* Passes callback the current time
* `requestAnimationFrame` does not create animations on its own, it's the sequence of successive callbacks on repaint that make things move on the screen
* pattern is to call `requestAnimationFrame` in a function recursively for some period of time
  * This period of time can be measured using the `Performance` API (i.e. `performance.now()`)
* `requestAnimationFrame` does not run on a separate thread, so animations may stutter under heavy CPU loads

```javascript
const element = document.querySelector("span");
const finalPosition = 600;

const time = {
  start: performance.now(),
  total: 2000
};

const tick = now => {
  time.elapsed = now - time.start;
  const progress = time.elapsed / time.total;
  const position = progress * finalPosition;
  element.style.transform = `translate(${position}px)`;
  if (progress < 1) requestAnimationFrame(tick);
};

requestAnimationFrame(tick);
```
