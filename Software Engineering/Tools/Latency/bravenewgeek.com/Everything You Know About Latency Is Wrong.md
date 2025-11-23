# [Everything You Know About Latency Is Wrong](https://bravenewgeek.com/everything-you-know-about-latency-is-wrong/)
* Latency is the time it takes one operation to occur
  * Every operation has its own latency
  * Latency cannot be measured as work units / time
* Latency almost never follows normal, Gaussian, Poisson distributions
  * Looking at averages, medians, and even standard deviations is useless
* Latency tends to be multi-modal attributable to "hiccups" in response times
  * "Hiccups" can be periodic freezes due to garbage collection pauses, hypervisor pauses, context switches, interrupts, database reindexing, cache buffer flushes to disk, etc
* The number one indicator you should never get rid of is the maximum value - that is not noise, that is the signal
  * By ignoring the max, you're effectively saying "this doesn't happen"

## How Many Nines?
* The media is the number that 99.9999999999% of response times will be worse than
  * Based on ~42 requested resources per page
  * Median Server Response Time = `(1 - (0.5 ^ # of resource requests per page)) x 100%`
 
## A Coordinated Conspiracy
* Common load-testing example is a client issuing requests at a certain rate, measuring the response times for each request, and putting them in buckets to analyze later
* Issue with this type of load testing is that if the operation under test took longer than the duration between tests
* If the next execution is delayed until the non-performant operation completes, then you've avoided measuring information when the system is underperforming
  * Coordination with the system by backing off
* Thought exercise: system that processes 100 requests/second at exactly 1 ms per request
  * Freeze system after 100 seconds of perfect operation for 100 seconds
  * Average response time over first 100 seconds is 1 ms
  * Average response time over next 100 seconds is 50 seconds
  * Average response time over the total 200 seconds is 25 seconds
  * 50th percentile is 1 ms
  * 75th percentile is 50 seconds
  * 99.99th percentile is 100 seconds
<img width="1908" height="1154" alt="image" src="https://github.com/user-attachments/assets/5906d9f4-6e41-4267-ba43-3097b0a70a23" />
* Measure system using load generator
  * 100 seconds at 100 requests/second for a total of 10k requests at 1 ms each
  * After stalling for 100 seconds, one result of 100 seconds
  * Average over the 200 seconds is 10.9 milliseconds (instead of 25 seconds)
  * 50th percentile is 1 ms
  * 75th percentile is 1 ms (instead of 50 seconds)
  * 99.99th percentile is 1 ms (instead of 100 seconds)
<img width="1850" height="1174" alt="image" src="https://github.com/user-attachments/assets/823e9611-b08d-47fa-940f-e877ff71a39f" />
* If X operations are measured in the first Y seconds, then X operations must also be measured in the latter Y seconds
* If system is improved such that instead of pausing for 100 seconds after 100 seconds of perfect operation, all requests are handled at 5ms each after 100 seconds
  * Calculation using load generator approach
  * 50th percentile is 1 ms
  * 75th percentile is 2.5 ms
  * 99.99th percentile is 5 ms
  * Bad data can make a better system look worse
* Note that in the load generator approach, after one bad (stalling) request, it will issue 9,999 good requests
  * So there's "double counting" as bad requests were replaced by good requests
* Coordinated omission shows service time and not response time
  * Service time is the time it takes a cashier to checkout a customer
  * Response time is the time it takes before a customer reaches the register
