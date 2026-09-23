# [If coding has been solved, why does software keep getting worse?](https://news.ycombinator.com/item?id=49033004)
* ["Imposter syndrome" was brought into the vernacular to normalize a lack of expertise](https://news.ycombinator.com/item?id=49043097)
  * "No one knows what they are doing so it's okay for you to also not know what you're doing"
* Interesting hypothesis from [this commenter](https://news.ycombinator.com/item?id=49033399): software engineer growth has been exponential such that 50% of all engineers have less than `n` years of experience (where `n` is small)
  * Because of this, most skills (practice) are honed practicing individual algorithms on a single thread. 
  * Working on the job, many of these engineers don’t understand the problems facing distributed systems
    * The getter/setter model is inadequate for many distributed systems
  * 10 years of practicing for-loops and data-structures does not translate into building a system that customers can access via their phone and multiple browser tabs, while some database schema is being updated
  * Focussing on the happy path only for the MVP is how to build buggy software
    * Good developers learn that this will necessitate a rewrite in the future
  * Interesting dichotomy between the evolution of languages, that increasingly abstract away concerns and focus on beginner usability while at the same time, tasks have become more complicated
