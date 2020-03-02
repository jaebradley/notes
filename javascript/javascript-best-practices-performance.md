# [JavaScript Best Practices - Performance](https://levelup.gitconnected.com/javascript-best-practices-performance-a549bc87ed20)

## Defer Script Loading

* Browser downloads file, parses the content, and then runs content
* Browser will download one file at a time, so it'll block
* Can run scripts after page is loaded by creating `script` elements on the fly and appending it
  * Anything that can be loaded after the page loads can use this method

```javascript
window.onload = () => {
  const element = document.createElement('script');
  element.src = 'some/src/url.js';
  document.body.appendChild(element);
}
```