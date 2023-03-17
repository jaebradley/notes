# Item 12: Consider implementing `Comparable`

* By implementing `Comparable`, a class indicates that its instances have a natural ordering
* `x.compareTo(y) == -y.compareTo(x)`, also implying that if the first throws an exception, the second should as well
  * If you reverse the direction of a comparison, the "sign" of that comparison should be "reversed" as well
* If `x.compareTo(y) > 0` and `y.compareTo(z) > 0` then `x.compareTo(z) > 0`
* `x.compareTo(y) == 0` implies `x.compareTo(z) == y.compareTo(z)`
* Strongly recommended, not required that if `x.compareTo(y) == 0` that `x.equals(y)` returns `true`
* `compareTo` is permitted to, and usually what should happen when two objects of different classes are being compared
* There is no way to extend an instantiable class with a new value component while preserving the `compareTo` contract
* If you want to add a value component to a class that implements `Comparable`, don't extend it
  * Instead, write an unrelated class containing an instance of the first class
  * Then provide a view method that returns this instance
  * This frees you to implement whatever `compareTo` method you like on the second class, while allowing its client to view an instance of the second class as an instance of the first class when needed
* `BigDecimal("1.0")` and `BigDecimal("1.00")` are not considered `equals` but are equivalent when compared using the `compareTo` method
* Compare object reference fields by invoking the `compareTo` method recursively
* Compare integral primitive fields using `>` and `<` but use `Double.compare` or `Float.compare` for floating-point fields
* If a class has multiple significant fields, the order in which these fields are compared is critical
  * Start with most significant field and work your way down
  * If comparison results in anything other than zero (i.e. equality), return the result
  * Else, need to compare next-most-significant field (until all fields are equal, or last field is non-zero)
* Can't just take integer diff in a `compareTo` since if one value is a large positive `int` and the other value is a large negative `int`, then `i - j` will overflow and return a negative value
