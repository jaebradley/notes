# [The current state of LLM-driven development](https://blog.tolki.dev/posts/2025/08-07-llms/)
* Agentic workflow is simple
  * Calls an LLM with a list of local HTTP servers it can query with JSON payloads
  * Waits for local server to respond to query-based HTTP request
  * Call the LLM again with the same context + the HTTP server's response
* MCP servers are roughly the same: offer access to existing data in a formatted way
* LLMs are text completion tools so the better formatted the search text is, the better the results
* New tools and models come out weekly - annoying for developers looking to build stable workflows
* Author noticed that LLMs do not perform well when interacting with code that has _not_ been written millions of times before
* Claude 4 outperforms Gemini 2.5 Pro and GPT 4 and 5 in agentic workflows
  * GPT 4 and 5 are not great, but are very good at following strict guidelines
* Author uses local models as a private personal assistant
* GitHub Copilot is great value for price, but heavily tied to VSCode
* Claude Code Pro is fully terminal-based
  * Glaude 4 Sonnet model is the same as GitHub Copilot (for twice the price)
* Hit Gemini Code's free 1000 requests in 10 minutes
* AI-first IDEs aren't great - opaque pricing, closed source prompts
  * Author things dedicated AI-first IDE is not the right tool for using LLMs
* LLM agents are good at writing Rust code
* On the flip side, Python is a poor fit for LLMs despite the vast amounts of training data
  * Author's found that strong Python typing makes LLMs much more helpful
* Author's found the following use-cases
  * Writing integration tests with seeded data
  * Sentry MCP can take an issue URL and fix the issue, if it not that complicated
  * LLMs are helpful when working with a new stack
    * Good at processing large amounts of documentation and implementing features based on that
* Author found that LLMs implementing core functionality built questionable implementations that required the author spending at least as much time rewriting the code as it would writing the logic from scratch
* Specifically, the author found frontend logic was lacking
  *  Models used magic numbers, keyboard interactions were poorly implemented, and some logic took 5+ prompts
*  Author wants "lighter" more-focused LLMs instead of the current approach of "run the biggest possible LLM and make it do everything"
