# [Handling Events](https://eloquentjavascript.net/15_event.html)

* Event handlers are given a context object (the `window` object or a DOM node)
  * Can assign an event handler using the `addEventListener` method
  * Can also use the `onclick` attribute for example, but only one function can be assigned to the `onclick` attribute
  * When removing an event handler using the `removeEventListener` method, the function defined must be the same function reference that was used for assignment
* Handlers registered on a node's parent will also receive events that happen on the node
  * Example is that a button in a paragraph will receive a click event as will the paragraph
  * Most specific handler receives event first
  * Event handler can stop propagation by using the `event.stopPropagation` method
  * Can use `target` property on the event object to ensure not accidentally processing event that shouldn't be handled
* Preventing event default behavior
  * Some nodes like links have default behavior (like following a link)
  * Event handlers are generally called before the default behavior occurs
  * So to prevent default behavior, call the `event.preventDefault` method
* DOM node of key event origination depends on element focussed on key press
  * If nothing is focussed, then the target is `document.body`
* For mouse events, the target element is the element directly below the mouse pointer
  * For the resultant `click` event that fires after a mouse event, it fires on the most specific node that contains both press and release of button
  * One paragraph is clicked, another paragraph is when click is released, so the click event happens on element containing both
* Scroll event handler is called after scroll takes place
* Focus and blur events do not propagate
* Page close / navigation triggers `beforeUnload` event
  * Return non-`null` value to prevent unloading to make browser show dialog to confirm redirection

```javascript
// Debouncing example for a text area that receives input events
// setTimeout schedules another function to be called

let timeoutId;
textarea.addEventListener('input', () => {
  clearTimeout(timeoutId);
  timeoutId = setTimeout(() => console.log('I am typing!'), 500);
});
```
