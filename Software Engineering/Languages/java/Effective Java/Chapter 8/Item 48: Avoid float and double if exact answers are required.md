# Item 48: Avoid `float` and `double` if exact answers are required

* `float` and `double` are particularly illsuited for monetary calculations because it is impossible to represent `0.1` or any other negative power of ten, as a `float` or `double` exactly
  * `1.03 - .42` -> `0.610000000....1`
* Use `BigDecimal`, `int`, or `long` for monetary calculations
