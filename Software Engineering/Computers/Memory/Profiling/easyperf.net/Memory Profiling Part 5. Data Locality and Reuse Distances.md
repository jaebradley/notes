# [Memory Profiling Part 5. Data Locality and Reuse Distances](https://easyperf.net/blog/2024/02/12/Memory-Profiling-Part5)

* Need to know both how many bytes are accessed during some period of X instructions and also if certain memory locations are accessed N number of times during this same period
* We also need to know the "distance" between when a memory location was reused
  * If this "distance" is less than the number of cache lines that the L1 cache can keep (~1000) then there is a good chace the data was reused efficiently
  * Otherwise, the cache line with the required data was probably evicted between first use and reuse
* Reuse distance - the number of unique memory locations that are accessed between two consecutive accesses to a particular memory location
  * Shows the likelihood of a cache hit for a memory access (given a typical LRU cache)
  * A reuse distance larger than the cache size will likely result in a cache miss

## Example

```
# cache line N
A | B | C

# cache line N + 1
D | E

# memory accesses over time
A (first time) | B (first time) | E (first time) | B (second time) | C (first time) | D (first time) | A (second time)
```

* Temporal reuse of calling A the second time is four, because there are four unique locations accessed between the two accesses to A (B, E, C, D)
* Spatial reuse is when different addresses on the same cache line are used and reused
  * In the example above, calling D is not a temporal reuse, but it has a spatial reuse distance of two
  * There were two accesses between when the same cache line was previously accessed (E) and when the cache line containing D was accessed
* If memory accesses has small temporal or spatial reuse distances, it is likely to result in CPU cache hits
* If memory accesses have large temporal AND large spatial reuse distances, it is likely to result in CPU cache misses