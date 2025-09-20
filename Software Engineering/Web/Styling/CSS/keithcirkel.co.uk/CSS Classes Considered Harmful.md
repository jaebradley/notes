# [CSS CLASSES CONSIDERED HARMFUL](https://www.keithcirkel.co.uk/css-classes-considered-harmful/)

## Scaling Class selectors
* If the `class` property is an analog to object-oriented classes, it's rare that there'd be a class with no parameters or no state
* Example `Card` component that can have various sizes, can be rounded, can be aligned a certain way
  * There can be another class for size (like `big`)
    * Some other CSS propety can co-opt what `big` means
    * One workaround is to combine selectors like `Card.big`, but this can cause specificity issues
  * Can have dedicated classes, like `BigCard`, but then this runs into trouble when taking into account other styles like `rounded` and `align`ment
  * Namespace class parameters like `Card--big`, but runs into issues when using the `Card--big` class outside of a `Card` component
* Mutually exclusive classes (`big` and `small`) can both be applied at once (`.Card.big.small`)
  * This is an inherent propblem with classes

## BEM is not the solution
* B(lock) E(lement) M(odifier) uses namespaces that prevent re-use issues
  * Introduces verbosity
* Problem still arises around applying mutually exclusive classes `class="Card--bit Card--small"`
* Because the class names can become verbose, modifying styling via JavaScript can also end up being verbose
 
```css
.Card { }
.Card--size-big { width: 100%; }
.Card--size-small { width: 25%; }
.Card--rounded { border-radius: 6px }
.Card--align-left { text-align: left }
.Card--align-right { text-align: right }
.Card--align-center { text-align: center }

.Card__Title { /* Sub components! */ }
```

## Atomic CSS is not the solution
* Utility classes / atomic CSS pivots from OOP concept of representing design system components like a `Card` component
* Classes are an abstraction of CSS properties
* To cut down on verbosity this system usually prefers short class names that are a handful of characters such as `br` instead of `border-radius`.
  * A big card looks like `<div class="w-big h-big al-l br-r"></div>`

## Potential solution

### Attributes
```css
.Card { /* ... */ }
.Card[data-size=big] { width: 100%; }
.Card[data-size=medium] { width: 50%; }
.Card[data-size=small] { width: 25%; }

.Card[data-align=left] { text-align: left; }
.Card[data-align=right] { text-align: right; }
.Card[data-align=center] { text-align: center; }
```

* Invariants are solved using HTML attributes that can only be expressed once, meaning `<div data-size="big" data-size="small">` will only match `data-size=big`
* Want to use some form of namespace (like the `data-` prefix)
* Attributes selectors allow you to treat the value as if it were a list like `data-border-collapse="left right"`

### Custom Tag Names
* `<my-card data-size="big"></my-card>`
* Completely valid HTML5 syntax

### Custom state using custom pseudo selectors
```css
my-card:state(loading) { background: url(./spinner.svg) }
my-card:state(loaded) { border: 2px solid green }
```
* Almost a form of internal state
