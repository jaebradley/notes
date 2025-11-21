# [10 Smart Performance Hacks For Faster Python Code](https://blog.jetbrains.com/pycharm/2025/11/10-smart-performance-hacks-for-faster-python-code/)
* `__slots__`  eliminate the need for a `__dict__`
  * Explicitly declare a fixed set of attributes for a given class via `__slots__`
  * Reduces memory usage and leads to marginally faster attribute access
  * (Obviously) restricts dynamic attribute assignment
```python
class Point:
    __slots__ = ('x', 'y')
    def __init__(self, x, y):
        self.x = x
        self.y = y
start = time.time()
points = [Point(i, i+1) for i in range(1000000)]
print(f"With slots: {time.time() - start:.4f}s")
# 0.12 seconds vs. 0.15 seconds without slots
```
* `math` module provides functions implemented in C
  * `math.sqrt` is typically faster and more accurate than using the exponentiation operator
  * About a `0.05` second improvement across 10 million iterations
* Pre-allocation helps reduce fragmentation and cache locality
  * `0.01` second improvement across a million element list
* The `itertools` module exposes efficient and optimized tools
  * `product`, `permutations`, and `combinations` generate results lazily
  * Generating a list of 100 tuples using `product` took `0.0005` seconds whereas double `for` looping through the same input list twice took `0.002` seconds (half an order of magnitude)
* `bisect.insort` to insert data into a sorted list in `O(log n)` instead of `O(n)` complexity via a linear scan
