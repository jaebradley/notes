# [Array Rotation - Programming Interview Problem 2](https://medium.com/@vojtastavik/array-rotation-programming-interview-problem-ii-92edaa421434)

* Rotate an array of `n` elements, `k` times
* `n` is `6` and `k` is `3` means `[1, 2, 3, 4, 5, 6]` is rotated to `[4, 5, 6, 1, 2, 3]`
* If `n` is `0` or `1`, no matter the number of rotations, the output will be the same
* When `k` is `0`, the number of rotations will be the same
* A negative `k` can be converted to it's a positive equivalent - `steps right = array length - k`
* When rotations are greater than the length of the array (i.e. `abs(k)` > `n`)
  * Can use modulo where the number of effective rotations is `k % n`
* The final calculation for rotations is `(length + k % length) % length`
  * `k % length` handles the case where `k` is some multiple of the array length
  * If `k` < `0`, then `length + k % length` handles converting the "left" rotation into a "right" rotation
  * If the "right" rotation is `>` `n`, then the final `% length` gets the "effective" rotations
  * This approach is `O(n * k)` as at each step of the final calculation of rotations need to move all elements of the array which is `O(n)`
* Another approach is 
  * Reverse entire array
  * Reverse first `k` numbers (where `k` is the calculated number of rotations)
  * Reverse the last `n - k` numbers
* So `[1, 2, 3, 4, 5, 6]` => `[6, 5, 4, 3, 2, 1]`
  * If `k` is `3`, then this becomes `[4, 5, 6, 3, 2, 1]`
  * Which then becomes `[4, 5, 6, 1, 2, 3]`
  * Reverse function is `O(n)` so time complexity is `O(3 * n)` => `O(n)`

