# [Ask HN: How can I get better at using AI for programming?](https://news.ycombinator.com/item?id=46255285)
* * If there's something that Claude tends to repeatedly get wrong, put it in the `CLAUDE.md` file
  * Claude always reads this file
  * [Try to keep it under 1k tokens](https://news.ycombinator.com/item?id=46256682)
* Plan mode (\`shift\` + `tab` 2x)
  * Talk to Claude until you like the plan before you let Claude execute
  * 2-3x's results for harder tasks
* Give the model a way to check its work
  * For example, use the Puppeteer MCP server and tell Claude to check its work in the browser
* [People transcribe thoughts into Claude Code](https://news.ycombinator.com/user?id=bogtog)
* From [this comment](https://news.ycombinator.com/user?id=dboon)
  * Don't try vibe-coding
  * Work with Claude to break task into verifiable chunks
  * Put the entire plan into a Markdown file
    * Task summary, individual plans to solve, references to source code files, verification points
    * Verifiability is a point of emphasis - unit tests
  * Loop through execution plan, reviewing code and committing when happy
