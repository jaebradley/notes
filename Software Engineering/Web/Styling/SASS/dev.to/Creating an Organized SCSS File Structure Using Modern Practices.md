# [Creating an Organized SCSS File Structure Using Modern Practices](https://dev.to/joxx/creating-an-organized-scss-file-structure-5dca)
* `@use` loads mixins, functions, and variables from other stylesheets and provides an explicit namespace
* `@forward` makes the public members of another module available to stylesheets that use your module
* Members are private by default and must be marked with `!default` to be configurable

## The Modern 7-1 Pattern
* Each folder contains an index file to manage its members
* "Abstracts" folder that contains things like variables, functions, mixins
* "Components" folder contains styles for individual UI components
* "Layout" folder has styles for major layout sections
* "Pages" folder contains page-specific styles
* "Themes" folder contains styles for specific visual themes ("dark" theme, "light" theme)
* "Base" folder contains global HTML element styles like typography
* "Venders" folder contains third-party styles for libraries, bootstrap, etc
* Optional "states" folder that contains active state styles, loading state styles, error state styles

## File Structure
```
scss/
  abstracts/
    _index.scss (forwards all abstract members)
    _functions.scss
    _mixins.scss
    _variables.scss
  base/
    _index.scss
    _typography.scss
  etc
  main.scss
```

* `_index.scss`  files `@forward` the other `scss` files in the same directory
* `main.scss` use `@use` instead of `@import`
