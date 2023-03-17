# [The Anatomy Of Slices In Go](https://medium.com/rungo/the-anatomy-of-slices-in-go-6450e3bb2b94)

* The "zero value" of a slice (i.e. `var s []int`) is `nil`
  * This is because a slice is a reference to an array
* If a slice references an array, and certain indices in the array change value, then the values in the slice will change as well
  * If a slice changes a value at the reference, that will also impact the array

```golang
var s []int
a := [...]]int{1, 2, 3, 4, 5, 6, 7}
s = a[2:4]

a[2] = 33
a[3] = 44

// s will now go from [3, 4] to [33, 44]
```
* Go provides a `cap` function to see what the capacity of a slice is
* A `slice` is actually a `struct` which has three fields
  * A `zerothElement` pointer which points to the first element in the array that the slice references
  * A `len` field that is the length of the slice
  * A `cap` field that is the capacity of the slice
  * When a new slice is defined the `zerothElement` pointer is set to `nil`
* The `append` function returns a new slice - it does not mutate the original slice
  * If `append`ing elements surpasses the capacity of the slice, a new array is created and the old array values are copied into the new array
* Slices also have a `spread` operator and can be used like `slice1 = append(slice1, slice2...)`
* The `extract` operator allows returning a `slice` from a `slice` like `slice1[1, 3]` (which will return a _new_ slice from index `1` to index `3` (exclusive))
  * Does _not_ include end index, if specified
  * Any slice created by the `extract` still references the same underlying array
* If slices need to be compared, need to loop through elements or use a `DeepEqual` function in the `reflect` package
