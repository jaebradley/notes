# [Nanosecond timestamp collisions are common](https://www.evanjones.ca/nanosecond-collisions.html)

* 5% of all samples, when reading the clock on all 4 physical cores at the same time had nanosecond collisions
* Unsafe to assume a raw nanosecond timestamp is a unique identifier
  * With 2 threads on a 4 core system, approximately 2% of tiemstamps collided
* OS X with microsecond absolute time resolution had many more collisions
* Even within a thread, the monotonic clock relative time measurement for each call to Go's `time.Now()` often did not increment
