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
