# [AI usage generates a fragile codebase](https://evanxmerz.com/post/ai-usage-generates-a-fragile-codebase/)

## [HackerNews Discussion](https://news.ycombinator.com/item?id=50438318)
* One [commenter](https://news.ycombinator.com/item?id=50450687) mentions how AI produces brittle code via overfitting
  * A human will try and produce a generalized solution, but an AI can deeply overfit some interface / parameter combination to produce a working outcome for the current set of conditions
  * However, if these conditions change, the existing implementation path will break
* Another [commenter](https://news.ycombinator.com/item?id=50444086) points out that code quality is not an issue, until it is
  * Everyone is happy while it is working, but then when symptoms arise, (broken functionality or even an increase in token usage due to an unnecessarily complex codebase) that's when people start caring