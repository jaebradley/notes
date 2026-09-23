# [The August 17 outage, and the work ahead](https://github.blog/news-insights/company-news/the-august-17-outage-and-the-work-ahead/)

## [From the HackerNews discussion](https://news.ycombinator.com/item?id=49378957)
* Commenter pointed out that [root cause is not “system component did not have enough capacity because of auto-scaling failures” but rather “this complex system collapses vs. degrading gracefully when demand exceeds capacity”](https://news.ycombinator.com/item?id=49384447)
  * There's no such thing as "infinite capacity" - when components reach capacity limits, excess traffic of the lowest priority should be rejected
  * Rejected traffic should not be retried, and rejected traffic errors should cause client-side throttling
  * Traffic isolation should occur: if the cause of the overload is a single client or system, no other system should be affected
* [Commenter](https://news.ycombinator.com/item?id=49380648) pointed out how "Errors in those services triggered a client-side retry loop that increased traffic during recovery" was symptomatic of a wider trend that avoids showing the user at any cost, even if it means watching a spinner forever
  * [When you have an outage, you should not retry at all](https://news.ycombinator.com/item?id=49381568)
    * RPC framework needs to communicate retryable vs. non-retryable failures so service A knows service B is dead and does not attempt to retry
