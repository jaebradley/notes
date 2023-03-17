# [What's Best: innerText vs. innerHTML vs. textContent](https://medium.com/better-programming/whats-best-innertext-vs-innerhtml-vs-textcontent-903ebc43a3fc)

```html
<div id='blog test'>
  This element is <strong>strong</strong> and     has some super fun <code>code</code>!
</div>
```

* Note the spacing between `and` and `has`

## `innerHTML`

* It returns the `HTML` / `XML` markup inside the string (like spacing and line breaks)
* If the text inside the element includes the characters `&`, `<`, or `>`, `innerHTML` will return these characters as HTML entities `&amp;`, `&lt;`, and `&gt;`
* `This element is <strong>strong</strong> and     has some super fun <code>code</code>!`

## `innerText`

* If a user highlighted contents of an element on their screen and copied it to their clipboard, `innerText` would return the contents of the elements
* `innerText` retrieves and sets the content of the tag as plain text, where `innerHTML` retrieves and sets the same content in HTML format
* So above is `This element is strong and has some super fun code!`

## `textContent`

* Aware of formatting like spacing and line breaks and will return those
* So above content is `this element is strong and     has some super fun code!`
  * `innerText` is aware of the rendered appearance of text, while `textContent` is not
