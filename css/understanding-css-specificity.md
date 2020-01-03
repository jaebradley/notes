# [Understanding CSS Specificity](https://medium.com/better-programming/understanding-css-specificity-d247add91810)

* Three different types of selectors
  * Selecting by id (gold)
  * Selecting by class name, attribute (like `a[target="_blank"]`) or pseudo-class (like `button:hover`) (silver)
  * Elements (like `nav`) and pseudo-elements (`p::first-line`)

```html
<html>

<head>
  <title>CSS Specificity</title>
</head>

<body>
  <div class="main">
    <p id="para">This is my first para</p>
  </div>
  <style>
    * {
      padding: 0px;
      margin: 0px;
    }
  </style>
</body>

</html>
```

* Can use the `*` selector to remove any browser-specific defaults
* In CSS, rule that is last in a CSS file takes precedence if multiple rules have same level of specificity
* 1 "gold" rule will take precedence over any number of "silver" or "bronze" rules. Similarly, any number of "silver" rules will override any number of "bronze" rules. 
  * The rule with the highest tally of selectors that are "gold", "silver", and "bronze" (in that order) takes precedence - in the event of a tie, the rule that occurs last, takes precedence
