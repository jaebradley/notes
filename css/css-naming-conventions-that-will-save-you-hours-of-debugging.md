# [CSS naming conventions that will save you hours of debugging](https://medium.com/free-code-camp/css-naming-conventions-that-will-save-you-hours-of-debugging-35cea737d849)

* Use hyphen-delimited strings (so `foo-bar` and not `fooBar`)
  * Pretty standard CSS naming convention and is consistent with CSS property names
* B(lock) E(lement) M(odifier) naming convention tries to solve the three problems associated with CSS naming
  * To know what a selector does just by looking at the name
  * To have an idea of where a selector can be used, just by looking at it
  * To know the relationships between class names, just by looking at them
* The example of a "stick man" with a `head` and `arms` elements, and maybe the "stick man" is sometimes blue or red
  * The "stick man" would be the block
  * The "head" and "arms" elements would be tacked on to the class name by adding two underscores
  * The blueness and redness of the stick man is considered a "modifier" and is tacked on to the class name by adding two hyphens

```css
.stick-man__head {

}

.stick-man__arms--blue {

}

.stick-man__arms--red {

}
```

* How do you indicate that a class name is used in JavaScript code?
  * Use a `js`-prefixed class names - so what was once `site-navigation` is changed to `site-navigation js-site-navigation`
  * Some people use the `rel` attribute with the idea being that the CSS has a _relationship_ with JavaScript and the `rel` attribute is leveraged to identify that
  * Some people use the data attribute, but probably not the best idea
