# [Essential JavaScript: Mastering Immediately-invoked Function Expressions](https://medium.com/@vvkchandra/essential-javascript-mastering-immediately-invoked-function-expressions-67791338ddc6)

```javascript
!function() {
    alert("Hello from IIFE!");
}();
// Shows the alert "Hello from IIFE!"
```

* When JavaScript sees `function` as first word in statement, it expects a function declaration (and not an expression)
* So to stop this from happening, add `!` - basically, makes JavaScript everything after `!` as an expression

```javascript
(function() {
  alert('I am not an iife');
});
```

* Another way to make a function expression is to wrap it in parentheses
* IIFE needs function expressions - function declarations can't be used for IIFEs
* IIFEs and function scope
  * Any variables declared in an IIFE are not visible to the outside world

```javascript
(function(a, b, c) {
  // do some stuff with a b c
}(a, b, c));
```

```javascript
var Sequence = (function sequenceIIFE() {

    // Private variable to store current counter value.
    var current = 0;

    // Object that's returned from the IIFE.
    return {
        getCurrentValue: function() {
            return current;
        },

        getNextValue: function() {
            current = current + 1;
            return current;
        }
    };

}());
```

* `current` variable is private - nothing but the functions that have access to it through closure can modify or access the `current` variable
