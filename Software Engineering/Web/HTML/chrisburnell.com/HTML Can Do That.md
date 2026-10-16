# [HTML Can Do That](https://chrisburnell.com/html-can-do-that/)
## `popover`
* No managing `z-index` `popover` and `popovertarget` attributes
```
<button popovertarget="example-popover">Toggle popover</button>
<div id="example-popover" popover>
	<p>No JavaScript, just modern browser magic, thanks to the wonderful folks speccing for the web and building our browsers!</p>
</div>
```

## `dialog`
* `command` / `commandFor` is another feature for opening and closing dialog elements without JavaScript
* Modal dialog boxes + popover
```
<button popovertarget="example-dialog">Toggle popover</button>
<dialog id="example-dialog" popover>
	<p>Closed with just HTML via <code>&lt;form method="dialog"&gt;</code>, opened with the <code>popover</code> attribute.</p>
	<button popovertarget="example-dialog" popovertargetaction="hide">Close</button>
</dialog>
```

## Grouped `<details>`
* A shared `name` attribute turns a group of `<details>` into an accordion where opening one `<details>` closes the others
```
<details name="example-group">
	<summary>First</summary>
	<p>Open the seond one and watch this close on its own.</p>
</details>
<details name="example-group">
	<summary>Second</summary>
	<p>First one’s hidden now.</p>
</details>
```

## `command` & `commandFor`
* Separate HTML buttons that control a single popover
```
<button command="show-popover" commandfor="example-command-popover">Open</button>
<button command="hide-popover" commandfor="example-command-popover">Close</button>
<dialog id="example-command-popover" popover>
	<p><code>show-popover</code> opens this and <code>hide-popover</code> closes it!</p>
	<button command="hide-popover" commandfor="example-command-popover">Close</button>
</dialog>
```

## Native input pickers
* Colour, range, and date pickers built into the browser
```
<label>Colour <input type="color" value="#5f8aa6" autocomplete="off"></label>
<label>Range <input type="range" min="0" max="100" value="50" autocomplete="off"></label>
<label>Date <input type="date" autocomplete="off"></label>
```

## `<datalist>`
* Native autocomplete suggestions that eliminate dropdown libraries
* Not really fully supported
```
<label>Favourite HTML element <input type="text" id="example-datalist-input" list="example-datalist" autocomplete="off"></label>
<datalist id="example-datalist">
	<option value="a">
	<option value="abbr">
	<option value="address">
	<!-- ... -->
</datalist>
```

## `loading=“lazy"`
* Image defers loading until it’s near the viewport
* Eliminates the need for `IntersectionObserver`
```
<img src="/images/avatar@2x.jpeg" loading="lazy" width="200" height="200" alt="Chris Burnell’s avatar.">
```

## HackerNews Discussion
* Commenter noted that out-of-the-box LLMs are not great at remembering these standards
  * This commenter referenced some LLM skills that are useful for modern CSS (https://news.ycombinator.com/item?id=49381627)
* `<datalist>` is not great if there needs to be a strong contract. User can type whatever they want into the field without fully filtering / typo mitigation
