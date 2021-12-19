# CPU Caches in the Big Picture

* Experience has shown that it is advantageous to separate the caches used for code and for data
* Processors hae multiple cores, and each core can have multiple threads
* Separate CPU cores have separate copies of almost all the hardware resources
  * The cores can run completely independently, unless they are using the same resource at the same time
  * Separate CPU cores have individual Level 1 caches, while threads share the same Level 1 cache
* Threads share almost all of the processor's resources
