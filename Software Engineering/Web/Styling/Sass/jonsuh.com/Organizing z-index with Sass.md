# [Organizing z-index with Sass](https://jonsuh.com/blog/organizing-z-index-with-sass/)
* Uses Sass `maps` to build a mapping between certain components and their `z-index` value

```scss
$z-index: (
  modal              : 200,
  navigation         : 100,
  footer             : 90,
  triangle           : 60,
  navigation-rainbow : 50,
  share-type         : 41,
  share              : 40,
);
```

* Should try and never declare `z-index` value in stylesheets directly but rather import the key-value pair in the mapping
  * Use built-in `map-get` function to access the desired `z-index` value
* Create a separate `_z-index.scss` to encapsulate this `z-index` map as well as custom functions and helpers
