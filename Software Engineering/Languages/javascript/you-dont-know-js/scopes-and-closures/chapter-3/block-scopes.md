# [Block Scopes](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch3.md#blocks-as-scopes)

* Block scopes are intended to restrict the exposure of variables
* In the following example, `i` pollutes the entire containing scope

```javascript
for (var i=0; i<10; i++) {
	console.log( i );
}
```

* `try` / `catch`es are blocked scoped to the `catch` block

## `let`

* `let` binds a variable declaration to it's containing block scope
* `let` does **not** get hoisted

```javascript
var foo = true;

if (foo) {
	let bar = foo * 2;
	bar = something( bar );
	console.log( bar );
}

console.log( bar ); // ReferenceError
```

* Using `let` in loops binds the loop variable (like `i`) to the body of the loop
* `let` also rebinds `i` to each iteration of the loop
* This means that `i` gets reassigned to the value from the end of the previous loop
* **`const` is also a block-scoped variable that cannot be reassigned**

## Block Scopes and Garbage Collection

* Sometimes variables don't get garbage collected when the belong to a closure that doesn't actual use that variable (i.e. they *could* get garbage collected)
* By using a block, the aforementioned variables don't belong to the closure

```javascript
// Imagine processing a big data set, then adding a click handler to a button like so

process(data) {
  // do some things
}

// This never gets garbage collected
// Even though it's not used after process(bigData)
// This is because the click function has a closure that contains bigData
var bigData = {..};

process(bigData);

var btn = document.getElementById( "my_button" );

btn.addEventListener( "click", function click(evt){
	console.log("button clicked");
}, false );
```

```javascript
process(data) {
  // do some stuff
}

{
  // anything in this scope is not accessible to closures in higher scopes
  var bigData = {..};
  process(bigData);
}

var btn = document.getElementById( "my_button" );

btn.addEventListener( "click", function click(evt){
	console.log("button clicked");
}, false );
```
