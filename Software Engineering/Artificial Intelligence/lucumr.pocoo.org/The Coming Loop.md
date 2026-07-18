# [The Coming Loop](https://lucumr.pocoo.org/2026/6/23/the-coming-loop/)
* Present-day models tend to produce code that is too defensive, too complex, too local in its reasoning
  * They avoid strong invariants, add fallbacks instead of making illegal states impossible
* Author does not see progressive improvment in this area
  * Instead, not having a human in the loop more often (and having an agent looping for X+ minutes) leads to worse code
* Models are “mortally terrified of exceptions”
* Make the malformed case unrepresentable / impossible to write in the first place
* LLMs will still attempt to handle impossible errors
* Amplified given this loop-based behavior as each iteration amplifies this pattern
* Author has had success with performance explorations where an LLM tries experiments, benchmarks them, discards failures, and keeps searching
* Author also believes most types of research aligns well with LLM abilities where a system can explore a complex problem space and report back without necessarily committing artifacts with a long anticipated shelf life

## [HackerNews Discussion](https://news.ycombinator.com/item?id=48643180)
* Loops work when there is understanding and thought about the problem space
  * Commenter mentions how it often times takes 5-6 sub-optimal implementations to gain full understanding
  * Commenter discusses how they shift between modes of "I don't know what I want", "I need to read/write/play with the implementation", "OK, I've done enough work where I think I know what I want"
  * Can't fake understanding or clarity and it is obvious when someone has skipped through the understanding
* Distinguish between "goal-driven work" where there is some end-goal state and there isn't any concern about _how_ the goal is achieved
  * Security exploits are an example where it is rare to are about the "code quality" of an exploit, just that one _can_ exploit a system
  * Taste-driven work where keeping a codebase amenable to future changes is often far more (at least from the perspective of the engineer) important than the specific feature
  * Maintainability and code quality are not synonymous: code quality is a means to an end where the end is maintainability
