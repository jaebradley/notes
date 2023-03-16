# Strings and Arrays

## Hash Tables

### Example Implementation

* Calculate hash for key - usually an `int` or `long`
  * There will be collisions since there are an infinite number of keys and a finite number of `int`s
* Map the hashcode to an index in the underlying array
  * This can be done like `hashCode % arrayLength`
* At every index, there is a linked list of key / value pairs
  * Add the new key to this linked list
* Given a key to retrieve a value for, calculate the hashcode, calculate the index, and traverse the linked list at the index until the key is found 
* If the number of collisions are very high (like all keys map to a single index) then retrieving a key will, in the worst case, by `O(N)`, where N is the number of keys (i.e. iterating through the linked list until the last element)
* If keys are well-distributed, the lookup time is `O(1)`

## `ArrayList` & Resizable Arrays

* `ArrayList` is an array with automatic resizing
* Typically, this resizing is implemented so when an array is full (or maybe even 80% full) the array doubles in size
* When the array doubles, the underlying elements need to be copied, so this takes `O(N)` time - however, it happens relatively infrequently, and so the amortized insertion time is still `O(1)`
* Why is the amortized insertion runtime `O(1)`?
  * If we have an array with size `N`, that means that the previous capacity increase copied `N/2` elements, and the one before that copied `N/4`, etc. etc.
  * The sum of this series is actually `N`
  * So inserting `N` elements takes `O(N)` work - each insertion is `O(1)` on average, even though some insertions take `O(N)` time in the worst case

### Applying `ArrayList`s to String concatenation

* Assume `n` strings all of size `x`
* We want to join all these strings together

```java
String combine(String[] words) {
  String sentence = "";
  for (String w : words) {
    sentence = sentence + w;
  }
  return sentence;
}
```

* The runtime of the above code is `O(xn^2)`
  * On each iteration a new `sentence` is created
  * When the new `sentence` is created, the strings are copied, character by character
  * First this is `x` characters. Then it's `2x` characters. Then it's `3x` characters.
  * In all, it's `O(x + 2x + 3x + ... + nx)` => `O(xn^2)`
* A better way to concatenate strings would be to store all the strings in an `ArrayList` and then copy them back to a string only when necessary
