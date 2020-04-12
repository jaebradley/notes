# Item 17: Use the `Array` Method To Convert `nil` And Scalar Objects Into Arrays

* If a method (or constructor) expets an array of values, but may want to be able to handle the case of a single input or even `nil` and handle these cases uniformly
* By using the `Array` method (_not_ the `Array` class) it'll convert a single argument into an array, `nil` into an empty array, and won't modify an array of values
  * If the argument to `Array` responds to `to_ary` or `to_a`, then that method is called
  * If neither methods exist, `Array` wraps the argument in a new array and returns it
  * This doesn't work for hashes as `Array(hash)` => `[[:key_1, :value_1], [:key_2, :value_2]]` since `Hash#to_a` is called
    * But if working with array of hashes, may want to use `struct` instead that would abstract away this logic (`Item 10`)