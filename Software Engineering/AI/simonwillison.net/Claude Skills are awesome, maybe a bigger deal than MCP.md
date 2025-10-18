# [Claude Skills are awesome, maybe a bigger deal than MCP](https://simonwillison.net/2025/Oct/16/claude-skills/)
* Skill is a Markdown file telling the model how to do something, optionally accompanied by extra documents and pre-written scripts that the model can run to help it accomplish the tasks descrbied by the skill
* Claude scans skill files and reads the YAML frontmatter in each skill Markdown file
* Example data journalism skills that can be built into a data journalism agent
  * Skill that describes how to build clean, readable data visualizations using D3
  * Where to get US census data and how to understand the data's structure
* MCPs use a lot of context tokens (GitHub's MCP consumes tens of thousands of tokens)
* Author sees skills as being extensible
  * Point other LLM (Codex / Gemini) at `SKILL.md` files without the skills having much knowledge of the skills system

