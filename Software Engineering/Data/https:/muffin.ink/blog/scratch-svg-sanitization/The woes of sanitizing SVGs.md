# [The woes of sanitizing SVGs](https://muffin.ink/blog/scratch-svg-sanitization/)

## `<script>` tags
* SVGs can contain `<script>` tags (cross-site scripting or XSS)
* Regex to find and eliminate `<script>` tags was not resistant to a capitalized `<SCRIPT>` tags as the regex fix put into place was case-sensitive

## JavaScript
* Other ways of embedding JavaScript in an SVG, like using an inline event handler
```svg
<img
  src="some invalid URL"
  onerror="alert(1)"
/>
```
* Used `DOMPurify` to remove scripts from the SVG

## `href` can trigger external requests
* Can use the `href` property on an `<image>` element to execute an external request
  * This means that the IP address of anybody loading the image could be logged without any explicit user behavior, like clicking on a link
* DOMPurify removes executable code, but does not protect against HTTP leaks
```svg
<image xlink:href="https://example.com/ping/" />
```
* Remove `href` properties from all elements if the URL refers to a remote website

## CSS `@import`
* Using a CSS `@import` statement inside of a `<style>` element can invoke external requests
```svg
<style>
  @import url("https://example.com/ping");
</style>
```
* Fixed by using a CSS parser to identify `@import` statements, strip them out, and then replace the original CSS without the `@import`ed statements

## HTTP leak via CSS `url()`
* Using `url()` inside certain CSS rules can invoke external requests
```svg
<rect style="background-image: url(https://example.com/ping)" />
<style>
  .img {
    background-image: url("https://example.com/ping");
  }
</style>
```
* Fixed by searching for `url()` and removing any styles or attributes referencing external URLs

## Alternative approaches
* Sandbox the SVG inside of an `iframe`
* Create an `iframe` with a `sandbox` property of `allow-same-origin`
  * This will block script execution inside the `iframe`
* Set up the `iframe` with the following HTML
```html
<meta http-equiv="Content-Security-Policy" content="default-src 'none'; style-src 'unsafe-inline' data:; font-src data:; img-src data:">
```
* The inline `Content-Security-Policy` will block all scripts
  * `default-src 'none'` tells the browser that no resources may be loaded by default
    * [This locks down all resource loading, allowing further directives to open up the policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Content-Security-Policy/default-src#no_inheritance_with_default-src)
  * `style-src 'unsafe-lineline'` allows inline JavaScript 
  * `data:` refers to the `data` [URI scheme](https://developer.mozilla.org/en-US/docs/Web/URI/Reference/Schemes), which is [data directly embedded in the URL](https://developer.mozilla.org/en-US/docs/Web/URI/Reference/Schemes/data)
* Additional benefit is that the SVG is trapped inside an `iframe`, and cannot restyle anything outside the `iframe` (like the rest of the page)