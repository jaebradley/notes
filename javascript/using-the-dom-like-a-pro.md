# [Using the DOM like a Pro](https://itnext.io/using-the-dom-like-a-pro-163a6c552eba)

* `document.querySelectorAll` will return a *static* `NodeList`
  * can execute `forEach` on a `NodeList` but not `map`, `reduce` (other Array methods)
  * convert to array using desctructuring or `Array.from`
  * `getElementsByClassName` return `HTMLCollection` vs. `NodeList`
* `HTMLCollection` is a "live" collection whereas `NodeList` is static
  * `HTMLCollection`s are updated when relevant elements are removed from the DOM
  * `NodeList`s are not
* `insertAdjacentHTML` allows arbitrary valid HTML insertion into DOM at four places
  * `beforebegin` - before the element
  * `afterbegin` - inside element before first child
  * `beforeend` - inside element after last child
  * `afterend` - after element
* `replaceWith` can replace existing element with new element created through `document.createElement` or an element in document
  * If element in document, it will be moved, not copied
* `DomParser` object and method `parseFromString` allows ability to parse `HTML` or `XML` source code into DOM document with only one element and then use that one element

```javascript
function createElement(domString) {
  return new DomParser().parseFromString(domString, 'text/html').body.firstChild;
}

const a = createElement('<a href="/home" class="active">Home</a>');
```

* Observer changes to DOM node like text changes, nodes being added / removed, or changes to node attributes through `MutationObserver`
  * `MutationObserver` is instantiated with a callback function that is executed whenever a change is observed
  * Use `observe` method on a specific DOM target
  * `MutationRecord`s will be passed to callback with things like type of change, element that changed, nodes that changed, attribute that changed
  * Disconnect observer after use

