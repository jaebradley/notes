# [Async And Defer: The Complete Guide To Loading JavaScript Properly](https://medium.com/javascript-in-plain-english/async-and-defer-the-complete-guide-to-loading-javascript-properly-ce6edce1e6b5)

* If there's JavaScript in a `script` tag that's part of a `head` that depends on the DOM (like `console.log`ging a DOM node) the DOM node won't exist
  * When the browser encounters a `script` tag, it is immediately loaded and executed and blocks construction of the DOM

```html
<head>
  <script>
    // outputs null
    console.log(document.querySelector('h1'))
  </script>
</head>
<body>
  <h1>our headline</h1>
</body>

<script>
  // outputs the H1 element since browser has loaded the hq at this point in time
  console.log(document.querySelector('h1'))
</script>
```

* Scripts that need to access DOM should only be loaded and executed when DOM is ready
  * Downside to this is that there's some time where the browser "looks ready" but JavaScript is still executing
* Using the `async` attribute in a `script` tag means that the script is loaded in parallel to all other resources and the browser can build the DOM and load the script simultaneously
  * When the script is _executed_ it will block the browser
  * So fetching the script can be parallelized with parsing the HTML document but parsing is blocked when the script is executed
  * However, this can cause problems when script execution depends on accessing something in the DOM
  * Also, problems when several scripts are loaded with `async` that actually depend on each other (like `script1` that `script2` depends on)
  * Can use `async` for 3rd party scripts for analytics like GA as well as scripts that don't need to directly access the DOM
* * Using the `defer` attribute does not block the browser when loading / fetching the script
  * However, script execution only occurs when the DOM is ready
  * Scripts will also get executed in the order in which they're defined
    * So unlike `async`, if `script1` is `defer`red and `script2` is `defer`red then `script1` will execute before `script2` - this is useful for interdependent scripts