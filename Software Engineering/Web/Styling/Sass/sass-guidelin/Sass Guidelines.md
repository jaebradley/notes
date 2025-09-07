# [Sass Guidelines](https://sass-guidelin.es)

## Sass or SCSS
* SCSS is short for Sassy CSS - if it's valid CSS, it's valid SCSS

## Strings
* It is highly recommended to force UTF-8 encoding in the main stylesheet by using the `@charset` directive
  * Make sure it is the first element of the stylesheet and that there are no characters before it
* Strings should always be wrapped in quotes, like `$direction: 'left'`

## Numbers
* When dealing with lengths, a `0` value should never have a unit
  * So `$length: 0;` is preferred over `$length: 0em;`
* Top-level numeric calculations should always be wrapped in parentheses

## Colors
* CSS color keywords should not be used
  * Keywords are not perfectly semantic (`grey` is darker than `darkgrey`)
* HSL representation is probably the easiest to understand
* Avoid hexadecimal as it is indecipherable when reading

## Selector Nesting
* Avoid selector nesting as it makes code more difficult to read
  * Reader has to calculate the resulting selector based on the indentation levels
  * Exceptions are pseudo-elements and classes

## Naming Conventions
* Stick to lowercase hyphen-delimited names
* Uppercase snake-case variables for constants
* If you intend to distribute your Sass code (like a library) you might want to consider namespacing variables, functions, mixins, etc
  * For example, if you work on a _Sassy Unicorn_ project, you could use the term `su-` as a namespace

## Components
* Components should not depend on its position in the DOM (when in the footer, sidebar, main content, etc)
* Components should exist in their own Sass partial (like `components/_button.scss`)
* Styles should only be concerned with
  * the styling of the component itself
  * the styling of the component's variants, modifiers, and states
  * the styling of the component's descendents (children), if necessary
* If you want your components to be themed externally, limit the declarations to only the structural styles
  * Dimensions (width/height), padding, margins, alignment
* Avoid referencing component files from other component files as this can tangle your project's dependency graph

## Naming Breakpoints
* Media queries should not be tied to specific devices
  * Breakpoints should not be named after devices
* Breakpoint map function like

```sass
/// Responsive breakpoint manager
/// @access public
/// @param {String} $breakpoint - Breakpoint
/// @requires $breakpoints
@mixin respond-to($breakpoint) {
  $raw-query: map-get($breakpoints, $breakpoint);

  @if $raw-query {
    $query: if(
      type-of($raw-query) == 'string',
      unquote($raw-query),
      inspect($raw-query)
    );

    @media #{$query} {
      @content;
    }
  } @else {
    @error 'No value found for `#{$breakpoint}`. '
         + 'Please make sure it is defined in `$breakpoints` map.';
  }
}
```

* Put media queries within selectors
  * While this produces duplicate media queries in the CSS output, this doesn't matter once the CSS is compressed (like Gzip)

```sass
.foo {
  color: red;

  @include respond-to('medium') {
    color: blue;
  }
}
```

## `!default` Flag
* All configuration variables should be defined with the `!default` flag so they can be overridden

```sass
// Library
$baseline: 1em !default

// Configured stylesheet
$baseline: 2em;
@import 'the previous library';
// baseline should be 2em
```

## Extend and Media Queries
* You should only extend selectors within the same media scope (`@media` directive)
* If two selectors are characteristically similar, might be a good use-case for `@extend`
* If two selectors are unrelated but share some rules, a `@mixin` might be better
