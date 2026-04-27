# [The 80% Problem in Agentic Coding](https://addyo.substack.com/p/the-80-problem-in-agentic-coding)
* Writing code and reading code are different cognitive capabilities
* You can review code competently even after your ability to write it from scratch has atrophied - this is *comprehension debt*
  * Over time, you may understand less of your own codebase
* "The agent implements an amazing feature and got 10% of the thing wrong, and you're like 'hey I can fix this if I just prompt it for 5 more minutes'. And that was 5 hours ago."
* If your ability to read code doesn't scale with an agent's ability to write / output code then you're not engineering anymore - you're hoping
* Teams with high AI adoption merged 98% more PRs but also review times ballooned 91%
  * PR size increased 154% on average
* At the time of writing, there seemed to be a bimodal distribution, and not a bell curve
  * 44% of developers write 90+% of their code manually
  * While there are others that ship dozens of PRs with 100% AI-written code
* Stack Overflow's 2025 survey showed that only 16% reported "great" productivity improvements while only half saw modest gains
  * "AI solutions that are almost right, but not quite"
  * "Debugging AI code takes longer than writing it myself"
* Don't tell AI what to do - give it success criteria and watch it loop
  * "LLMs are exceptionally good at looping until they meet specific goals and this is where most of the 'feel the AGI magic' is to be found"
* Imperative (old model): "Write a function that takes X and returns Y. Use this library. Handle these edge cases. Make sure to...."
* Declarative (new model): "Here are the requirements. Here are the tests that must pass. Here is the success criteria. Figure out how."
* Patterns that work
  * Write tests first and let the agent iterate until they pass
  * Let the AI introspect a client (like a browser) via MCP and let the AI verify behavior visually
  * Implement the naive correct version and let the AI optimize while preserving correctness
  * Define the API contract and let the AI implement the contract
* Have the model code review its code using a fresh context window
* Slopacolypse is driven by people who mistake velocity for productivity
  * Automated verification at as many steps as possible (linting, type checking, tests)
  * Deliberate constraints on agent autonomy
  * Human-in-the-loop during architectural decision points
