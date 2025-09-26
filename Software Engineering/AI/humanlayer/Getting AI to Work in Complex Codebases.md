# [Getting AI to Work in Complex Codebases](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/ace-fca.md)
* Sean Grove argues that the idea of chatting to an AI agent for two hours, specifying what you want, and then throwing it away while committing the final source code is like compiling source code and checking in the compiled binary while throwing away the source code
* In the AI future, the prompts and specs will become the "real" code
* People will open Python files with the same frequency that people open an editor to read assembly
* AI tools often lead to a lot of rework, diminishing perceived productivity gains
* Thus, AI tools tend to work well for greenfield projects, but are often counter-productive with established codebases and complex tasks

## Frequent Intentional Compaction
* Designing development process around context management
* Utilization in 40-60% range
* Inject high-leverage human review at the right points
* Costs 3 developers around $12k per month using Opus

## Naive Way to Manage Agent Context
* Use coding agent like a chatbot
* Next step is to start over when, using a new session with more context in the prompt
  * "`original prompt`, but make sure to use XYZ approach, because ABC approach won't work"

## Intentional Compaction
* "write everything we did so far to progress.md, ensure to note the end goal, the approach we're taking, the steps we've done so far, and the current failure we're working on"
* Searching for files, understanding code flow, applying edits, test/build logs, large JSON blobs all eat up context
* The contents of the context window are the only level to affect the quality of the output
* The worst things that can happen to the context window is filling it with incorrect information, missing information, or too much noise
* [Research prompt](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/research_codebase.md)
  * Can use custom subagents but can use the Claude Code Task tool with the `general-agent` 
* [Planning prompt](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/create_plan.md)
* [Implementation prompt](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/implement_plan.md)

## Eggs on Faces
* Author also tackled a context-rich problem (Hadoop dependencies) and the intentional compaction flow did not work well
* Research steps did not go deep enough through the dependency tree and the implementation assumed classes could be moved upstream
* Author notes that there are problems that cannot be prompted through
* Author notes that you probably need at least one person who is an expert in the codebase

## On Human Leverage
* In the intentional compaction flow, a bad line of research leads to a bad plan and many lines of bad code (i.e. the blast radius of bad research can be quite high)
* So the research and plan prompting is the most important parts to get correctly
