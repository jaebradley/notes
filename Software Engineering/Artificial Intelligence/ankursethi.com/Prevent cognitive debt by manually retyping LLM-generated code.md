# [Prevent cognitive debt by manually retyping LLM-generated code](https://ankursethi.com/blog/prevent-cognitive-debt-by-manually-retyping-llm-generated-code/)
* Author uses coding assistants to implement "boring" functionality / features
* However, the author doesn't enjoy reviewing AI-generated PRs as the author finds the logic overly-defensive, poorly-commented, and subtly incorrect
* Author believes that personal projects must be fun above all else
  * "The joy of working on personal projects comes from the process, not from the outcome"
* Author's solution is "grossly inefficient and perhaps slightly comical" - the author asks the coding assistant to generate the code and then the author manually makes all the edits themselves
* Author finds that instead of being, say, 10x faster, they're only 2x faster
  * However, the tradeoff in speed is made up with gains in comprehension / understanding
* Author continues to have a spatial map of the codebase - the author knows where every bit of functionality exists
  * This leads to speedups when working in a project
* The pre-LLM analogue was the advice to never copy-and-paste code - to actually understand the source code by typing it out (by hand) and adapting it to the current use case / understand it completely
* Author fears that the software industry has a large amount of cognitive debt that the industry will need to pay back at some point
* Author views not completely understanding their authored software as professional malpractice

## [HackerNews Discussion](https://news.ycombinator.com/item?id=49153374)
* [Commenter questions whether the workflow of "think hard, let LLM write it, read what LLM wrote, re-type/fix what LLM wrote" where the efficiency gains are](https://news.ycombinator.com/item?id=49163104)
  * [In response, another commenter mentions that LLM "efficiency gains" have always been predicated on not understanding the produced code](https://news.ycombinator.com/item?id=49164111)
    * Reviewing well-written code to understand it has always taken longer than simply writing it yourself
    * People are realizing that the most valuable commodity is engineers understanding what is going on and that the loss of understanding a codebase is the primary blocker to getting things done
    * Commenter suspects that this is why long-term LLM productivity stats have not been seen - if one takes away the pressure to produce bad code, writing by hand wins in the long-term
