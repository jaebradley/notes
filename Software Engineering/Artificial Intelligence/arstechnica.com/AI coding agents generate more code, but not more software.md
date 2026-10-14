# [AI coding agents generate more code, but not more software](https://arstechnica.com/ai/2026/10/ai-coding-agents-generate-more-code-but-not-more-software/)

## [HackerNews Discussion](https://news.ycombinator.com/item?id=50059523)
* ["Quality was always a significant bottleneck and is even more so with agentic coding."](https://news.ycombinator.com/item?id=50061356)
* [One commenter](https://news.ycombinator.com/item?id=50064118) mentioned that every RCA process they've dealt with had something to the effect of "AI-generated tests asserted that the defective behavior was correct"
* [One commenter](https://news.ycombinator.com/item?id=50061811) cited Microsoft studies
  * > Microsoft study of 484 developers, published in 2025 says 11%.
    > Another Microsoft study where programmers had telemetry installed showed 21%
* [One commenter](https://news.ycombinator.com/item?id=50062984) mentioned that the biggest rework cost that happens during code review is that feature was insufficiently implemented
  * > For example, the feature, as implemented, doesn’t sufficiently solve the original business problem. Or it solves it in a bad way, like because of overengineering or taking on unnecessary technical debt. I haven’t really seen much evidence of AI code review catching either of these problems.
    > I’ve heard people complain that human code review doesn’t reliably catch them, either. And I have seen that with my own eyes. Just not universally. Teams that have a strong culture of shared ownership and collaborating on designs regularly catch these kinds of problems in code review. If they’re really good at it, they catch them even earlier. e.g., I know planning poker isn’t fashionable anymore, but when I’ve been on teams that take it seriously they tend to be pretty good at finding minimally expensive, correct designs pretty early in the process
    > I think that this is the kind of thing that led to folks like DORA observing that AI coding is something of a multiplier for the effects of both good and bad software development practice. If you’ve got a cowboy coding culture, throwing AI into the mix will probably just lead to agentic cowboy coding.
* [One commenter](https://news.ycombinator.com/item?id=50063000) mentioned that they don't necessarily generate better code
  * LLMs tend to generate repetitive code, missing opportunities for extraction and refactoring
  * LLMs tend to over-comment, when it is not useful
  * Can fix a lot of these flaws by "shepherding" the process, but this can get tedious
