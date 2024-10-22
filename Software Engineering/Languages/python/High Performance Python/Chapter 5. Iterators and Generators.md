# Chapter 5. Iterators and Generators

* List comprehension will create a list of all the `value` items
  * Form: `[<value> for <item> in <sequence> if <condition> ]`
* Generator equivalent is `(<value> for <item> in <sequence> if <condition>)`
* Generators don't have a `length` property - instead, use `sum` for each element that is generated to get "length"
  * Increases compute time, but does not consume any additional significant memory

## Lazy Generator Evaluation

* Example of `groupby` function in `itertools`
  * `groupby` function outputs a generator of tuples
  * First element is the key for the group
  * Second element is a generator for the items in the group
  * Groups are only formed for data that is sequential
  * Grouping `A A A A B B A A`  by letter would lead to three groups `(A, [A, A, A, A])`, `(B, [B, B])`, and `(A, [A, A])`
  * So probably need to sort based on the key in order to get intended behavior

```python3
def groupby_window(data, window_size=3600):
    # returns iterator that returns first window_size elements from data
    # https://docs.python.org/3/library/itertools.html#itertools.islice
    window = tuple(islice(data, window_size))
    for item in data:
        # return window and then pause until following `next(groupby_window)` call
        yield window
        # next window is removing leading element and adding next element in data, starting from the beginning of data
        # I'm not sure this is the intent of the author
        window = window[1:] + (item,)
```

* Memory footprint has an upper bound of `window_size`
* Optimization is to use a `deque` to get `O(1)` appends and removes on both ends of the list
  * Normal lists have `O(n)` appends / removes from the beginning of the list
  * However, because these operations are in-place, they would "destroy" any previous views of the rolling window

