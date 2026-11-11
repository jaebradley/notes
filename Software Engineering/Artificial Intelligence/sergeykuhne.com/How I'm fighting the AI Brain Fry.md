# [How I'm fighting the AI Brain Fry](https://sergeykuhne.com/blog/fighting-ai-brain-fry)
* Author mentions work pattern with AI where author starts a task, then while an LLM is working on that task, the author starts another task
  * The LLM from the first task needs clarification, when the LLM from the second task needs additional clarification, and the author finds themselves juggling multiple tasks and context boundaries
* Author's approach to this problem is to use a queue that lives in `git`
* Every piece of work is a markdown file in the repository, and the state of work (i.e. "the Jira board") is whatever the state of the markdown files are at any given time
* Agents read these markdown files and start building pieces of work that have been marked by a human as safe to build
  * Agents also research new tickets, run tests, verify deployed behavior
* Agents never deploy to production
* Author / a human is the decision maker
  * Script reads all the markdown files and identifies a list of items waiting on human review / decision-making (e.g. review this change, answer this question, make a decision about this tradeoff)
  * A page is generated from the set of markdown files in `git` that lists the details for the highest priority item and the key decision waiting human input
  * Author's decision is noted in the markdown file and committed to `git`
* Author finds this process, and the act of putting an item back on the queue ("closing an item") "releases" the mental memory / resources associated with working on that item
  * Author ends up focusing on one item at a time 

## [HackerNews Discussion](https://news.ycombinator.com/item?id=50416363)
* [Commenter](https://news.ycombinator.com/item?id=50417688) spends several days building detailed architecture documents
  * Create artifacts with these detailed plans for each isolated feature
  * Then uses many agents to refine and ultimately implement these features
* [Commenter](https://news.ycombinator.com/item?id=50417750) mentions that every review identifies code "overfitting" and brittle solutions with tacked on solutions
  * [Commenter](https://news.ycombinator.com/item?id=50419862) defines "brittle" as building an ever-increasing number of abstractions
* [Commenter](https://news.ycombinator.com/item?id=50420972) uses Starcraft as an analogy for LLM-driven programming
  * Starcraft is a real-time strategy game where there isn't time to think too deeply
  * Starcraft rewards right intuition and speed over deep thinking because attention is the most valuable resource and attention is demanded by dozens of different simulatenous tasks / competing objectives
  * Commenter identifies Starcraft training as a way they built ability to have very intense focus for long periods of time
* [Commenter](https://news.ycombinator.com/item?id=50416698) identifies an interesting analogy between how people exercise for sport due to the automation of manual labor and how those that do not exercise most likely don't have adequate physical health
  * The parallel is that LLMs automate away thinking, with the risk that brains will look and feel the way our bodies would feel if we didn't actively work them 
