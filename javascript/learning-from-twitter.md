# [Learning From Twitter](https://johnresig.com/blog/learning-from-twitter/)

* Don't attach handlers to window scroll event
  * Scroll event can fire very frequently so adding a callback will slow attempts to scroll page
  * Alternative is to use a timer to check every X milliseconds or to attach scroll handler and run code after delay
* Cache query selectors that are being reused
  * Don't need to requery DOM after every scroll event if scrolling doesn't modify DOM

```javascript
var container = $.find('#someContainer');
var scrolled = false;

$(window).on('scroll', function() {
  scrolled = true;
});

setInterval(function() {
  if (scrolled) {
    scrolled = false;
    // load more results
  };
}, 250);
```
