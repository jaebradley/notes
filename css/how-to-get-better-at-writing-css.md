# [How To Get Better At Writing CSS](https://medium.com/@thomlom/how-to-get-better-at-writing-css-a1732c32a72f)

* Author mostly talks about SCSS and best practices around SCSS organization / definition
* Create partials by naming the file with a leading underscore (e.g. `_animations.scss`)
  * SCSS is smart enough to know that `@import x` refers to partial file `_x.scss`

```scss
// _animations.scss
@keyframes appear {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
// header.scss
@import "animations";
h1 {
  animation: appear 0.5s ease-out;
}
```

## BEM (Block-Element-Modifier) Methodology

* Blocks are just components like a card, or a form, or a post
* Elements compose a block but are useless or don't make sense outside context of a block
  * Convention is `.block__element` like `.post__author` or `.post__date`
* Modifiers represet flags on a block or element that are used to change behaviors, appearances, and so on
* Should only use classes for BEM and not ids or tags
* Blocks and elements can be nested in other blocks and elements but they should be completely independent
  * So don't put margins on a button because it should be under a title - these two blocks will be related to each other
  * Use a utility class instead to add margin

## Organization CSS Files (7-1 Pattern)

* Write partials in 7 different folders
  * `base` - all boilerplate code like typography rules, animations, utilities (classes like `margin-right-large`, `text-center`)
  * `components` - pretty self-explanatory
  * `layout` - styles for header, footer, navigation, section, grid
  * `pages` - any time a page has its own specific styling
  * `themes` - if there are different themes in the app (dark mode or admin mode)
  * `abstracts` - functions / variables / mixins
  * `vendors` - external library styling like Font Awesome files, Bootstrap
* Import all of them in one `main.scss` folder
* For smaller projects, put external CSS code in a `link` tag placed in the `head` element
  * Probably will only have one theme for the entire app, so don't need different themes
  * Probably don't need page-specific styles too
  * Can also just have one `sass` folder where all the partials are kept in files (`_animations.scss`, `base.scss`, `_variables.scss`)

## Autoprefixer

* When building a website, you may use features that are not fully supported on all browsers - thus, need to add vendor prefixes
  * This makes CSS compliant with browsers without needing to write out all this prefixing

```css
-webkit-animation-name: myAnimation;
-moz-animation-name: myAnimation; 
-ms-animation-name: myAnimation;
```

* Compile SCSS => one main CSS files
* Add prefixes to CSS file using Autoprefixer
* Compress CSS file