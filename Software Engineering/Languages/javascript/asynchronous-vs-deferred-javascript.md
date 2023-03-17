# [Asynchronous vs. Deferred JavaScript](https://bitsofco.de/async-vs-defer/)

* JavaScript is considered a parser blocking resource - this means that the parsing of the HTML document itself is blocked by JavaScript
  * When the parser reaches a `<script>` tag whether it's internal or external, the parser stopes to fetch it (if it's an external resource) and execute it
* If there are several JavaScript files on a page, this will interfere with the time to first paint even if the document is not actually dependent on those files
* The `async` attribute is used to indicate to the browser that the script file _can_ be executed asynchronously meaning that the downloading of the JavaScript can occur while the HTML is parsing
  * Only available to externally located script files
  * Once the JavaScript is downloaded, parsing is paused for the script to execute
* The `defer` attribute tells the browser to download the file while HTML is being parsed, but to execute the script after parsing is complete
* Asynchronous and deferred execution is important when the `script` element is located at the beginning of the HTML file
  * Placing it later in the file means that the parser has finished more of the document body, meaning loading JavaScript blocks less of any remaining parsing
* If a script file is not dependent on any other files or doesn't have any dependencies, using `async` is useful
* If the script relies on the DOM, `defer` should be used since it will be executed after all the HTML has been parsed
