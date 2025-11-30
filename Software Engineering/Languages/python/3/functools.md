# [functools](https://docs.python.org/3/library/functools.html)
* `@functools.cache`
  * Same as `lru_cache(maxsize=None)`
  * Cache is threadsafe
* `@functools.cached_property`
  * Instance method is transformed into a property
  * This property is computed once and then cached as a normal attribute for the life of the instance
  * `cached_property` does not prevent a possible race condition in multi-threaded usage
    * Getter function could run more than once on the same instance, with the latest run setting the cached value
    * If the cached property is idempotent then this is not harmful
* `@functools.lru_cache`
  * Cache is threadsafe and can be utilized in multiple threads
  * A dictionary is used to cache results so the positional and keyword arguments to the function must be hashable
    * `f(a=1, b=2)` and `f(b=2, a=1)`  differ in their keyword argument order and may be considered distinct
  * If `maxsize` is set to `None`, the LRU feature is disabled and the cache can grow unbounded
* `@functools.reduce`
  * First argument is the accumulator function
    * First argument of the accumulator function is the accumulated value
    * Second argument is the current value in the iterable
  * Second argument is the iterable
  * Optional final argument is an initial value if the iterable is missing
