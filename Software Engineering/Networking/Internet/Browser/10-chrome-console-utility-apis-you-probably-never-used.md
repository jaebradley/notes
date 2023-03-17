# [10 CHrome Console Utility APIs You Probably Never Used](https://blog.bitsrc.io/10-chrome-console-utility-apis-you-probably-never-used-14a0b64f1bd6)

* `monitor` is a function to know when another function 
  * `monitor(someFunction)`, and then when `someFunction` is called, `monitor` will log that `someFunction` was executed and with what arguments
* `monitorEvents` logs console messages when specific events occur on the specified object
  * `monitorEvents(button, "click")` will log when the `button` is `click`ed
  * The defined events can also be an array of events so `["click", "mouseover"]` or an array of "generic" events like `["click", "mouse"]`
  * `"mouse"` is a generic event for `mouseover` and `mouseout`
* `$_` recalls the last executed expression in the console
* There is a `copy` function that copies the argument to the clipboard
* Similar to `Object.keys` and `Object.values` there is a `keys` and `values` function
* `getEventListeners` returns the event registered on an object / DOM element
* `$$` returns the elements that are specified so `$$('button')` will return all `button` elements
