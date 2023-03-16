# `ToPrimitive`

* Abstract operation from `ES5` specification, section `9.1`
* Checks to see if it has `valueOf`
* If `valueOf` is available and returns a primitive, it uses that value for coercion
* If `valueOf` is unavailable, but `toString` is available and returns a primitive, it will provide the value of `toString` for coercion
* If neither operation is available or provides a primitive, a `TypeError` is thrown
