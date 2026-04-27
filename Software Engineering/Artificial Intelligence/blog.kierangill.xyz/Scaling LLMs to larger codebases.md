# [Scaling LLMs to larger codebases](https://blog.kierangill.xyz/oversight-and-guidance)
* A prompt library is a set of documentaiton that can be included as context for an LLM
* Collate documentation, best practices, a general map of the codebase, and other context an engineer needs to be productive the codebase
* Every time the LLM is slightly off target, ask yourself "What could've been clarified?" and then add that answer back into the prompt library
* Author references the following mentality from the Cursor team: "Ultimately, the principles of clean software are not that different when you want it ready by people vs. models. Taste in code is going to become even more important as these models get better because it will become easier and easier to write / generate more code, and thus it'll be more and more important to structure the code in a tasteful way"
  * In constrast, the author's colleague at Meta told the author that Meta's codebase has so much technical debt that the utility of LLMs gets bottlenecked
* LLM literacy dipsticks:
  * Ask a peer engineer to read code they're unfamiliar with
    * If they have trouble understanding it, navigating it, understanding underlying modules and interfaces, any relevant side-effects, etc then so will an LLM
  * Ask an LLM how certain functionality works and follow the LLM's trail and document its snags
    * Notice how it tries to solve problems and try to make it easier for the LLM to identify critical documentation and navigate the codebase
