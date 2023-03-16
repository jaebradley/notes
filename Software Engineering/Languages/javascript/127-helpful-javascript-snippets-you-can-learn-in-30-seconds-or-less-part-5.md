# [127 Helpful JavaScript Snippets You Can Learn In 30 Seconds Or Less - Part 5](https://medium.com/better-programming/127-helpful-javascript-snippets-you-can-learn-in-30-seconds-or-less-part-5-of-6-e81e0736e69d)

* `minN` - returns the n smallest elements from a list.

```javascript
const minN = (arr, n = 1) => [...arr].sort((a, b) => a - b).slice(0, n);

minN([1, 2, 3]); // [1]
minN([1, 2, 3], 2); // [1,2]
```

* `randomHexColorCode`
  * `Number#toString` takes a base / radix (hence why `16` is passed to it)

```javascript
const randomHexColorCode = () => {
  let n = (Math.random() * 0xfffff * 1000000).toString(16);
  return '#' + n.slice(0, 6);
};

randomHexColorCode(); // "#e34155"
```

* `randomIntegerInRange`

```javascript
const randomIntegerInRange = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min;

randomIntegerInRange(0, 5); // 3
```

* `scrollToTop` - smooth scroll to top of page
  * `c` is the number of pixels the page is scrolled vertically
  * `requestAnimationFrame` will recursively call `scrollToTop` until at top of page
  * On each execution of `scrollToTop`, move the page an eighth of the way up the page

```javascript
const scrollToTop = () => {
  const c = document.documentElement.scrollTop || document.body.scrollTop;
  if (c > 0) {
    window.requestAnimationFrame(scrollToTop);
    window.scrollTo(0, c - c / 8);
  }
};
```