# [Why Writing Correct Software Is Hard](https://pron.github.io/posts/correctness-and-complexity)
* [Study from the University of Toronto](https://files.usenix.org/s3fs-public/cdn/osdi14-paper-yuan.pdf) found that a vast majority of catastrophic failures in large distributed systems was due to lack of thought when handling errors
  * > Almost all (92%) of the catastrophic system failures are the result of incorrect handling of non-fatal errors explicitly signaled in software in 58% of the catastrophic failures, the underlying faults could easily have been detected through simple testing of error handling code
  * By creating a linter for exception-handling logic, this would have prevented 1/3 of all catastrophic failures

## [HackerNews Discussion](https://news.ycombinator.com/item?id=34567890)
* [Commenter](https://news.ycombinator.com/item?id=50471641) makes the analogy between contract law and software and the fact that (many) lawyers exist and how legal loopholes are an issue in the same way that "bugs" are also an issue