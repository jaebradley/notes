# Awful Parts

## Global Variables

* There is a single common global object
* Global variables can be created by
  * Placing a `var` statement outside of a function
  * Assigning a property to the global object (`window.foo = 'foo'`)
  * Using a variable that doesn't exist

```javascript
// First example without implied global variable

var a = function () {
  var b = 'b';
  console.log(b);
};

// b is undefined

a(); // outputs 'b'

// b is still undefined

// Second example with implied global variable

var a = function () {
  b = 'b';
  console.log(b);
};

// b is undefined

a(); // outputs 'b'

b // equals 'b'
```

## Scope

**A variable declared ina block is visible everywhere in the function containing the block**

## [Semicolon Insertion](http://www.bradoncode.com/blog/2015/08/26/javascript-semi-colon-insertion/)

```javascript
// JavaScript interprets

return
{
  foo: 'foo',
};

// as

return;
{
  foo: 'foo',
};

// because of automatic semicolon insertion (a feature of JavaScript so that you don't have to always add semicolons everywhere)

// However, this means that when the { is not on the same line as the return, undefined is returned
```

## `typeof`

* `typeof` operator returns `object` for `null`
* Right way to check if a variable is an `object` or an `array` is since `null` is `falsy`

```javascript
var isObject = function (value) {
  value && typeof value === 'object';
}
```

## `parseInt`

* `parseInt` stops evaluation after the first nondigit, so `parseInt('100')` and `parseInt('100 bottles of beer')` evaluate to the same thing
* If the input string starts with a `0`, `parseInt` evaluates to base 8 - `parseInt` has an optional `radix` parameter that can be used to specify `base 10` like `parseInt('08', 10)`.

## `NaN`

* `NaN` is supposed to indicate that a value is "not a number"
* `typeof NaN` evaluates to `number`
* `NaN` !== `NaN`
* JavaScript does have an `isNaN` function
  * Can combine `isNaN` and `isFinite` functions to create a `isNumber` function that eliminates `NaN` and `Infinity`

```javascript
var isNumber = function(value) {
  return typeof value === 'number' && isFinite(value);
};
```

## Object emptiness

* Objects are rarely ever "empty" because they pick up members from the prototype chain
* This can matter when performing operations on object properties
* Contrived example using word counting

```javascript
var i;
var text = 'foo bar foo constructor';
var wordCounts = {};
var words = text.split(' ');

for (i = 0; i < words.length; i += 1) {
  var word = words[i];
  var wordCount = wordCounts[word];

  if (wordCount) {
    wordCount += 1;
  } else {
    wordCount = 1;
  }

  wordCounts[word] = wordCount;
}

// What wordCounts probably looks like:

// {
//   foo: 2,
//   bar: 1,
//   constructor: 'constructorFunctionDefinitionAnd1',
// }
```

* Because one of the words is `constructor` and `wordCounts.constructor` is already defined, when evaluating `wordCount += 1`, JavaScript appends `1` to the `wordCounts.constructor` function definition, which is a `string`
* Instead of checking for the `truthy`ness of `wordCount`, the appropriate check is something like `typeof wordcount === 'number'`
