# [How I Use Every Claude Code Feature](https://blog.sshh.io/p/how-i-use-every-claude-code-feature)
## `CLAUDE.md`
* Small `CLAUDE.md` to start, documenting changes based on mistakes Claude makes
* `@`-mentioning other documentation files bloats Claude's context window
  * Claude often ignores file paths
  * Have to detail *why* and *when* to read the file like "if you encounter a `FooBarError`, see `path/to/foobarerror/docs.md`"
* Avoid negative-only constraints or else the agent will get stuck when it thinks it *must* use the flag

## Custom Slash Commands
* `/catchup`: prompts Claud to read all changed files in the current git branch
* `pr`: cleans up code, stages it, and prepares a pull request

## Custom Subagents
* If complex task `A` requires `X` tokens of input context, accumulates `Y` tokens of working context, and produces a `Z` token answer, then running `N` tasks means `(X + Y + Z) x N` tokens in the main window
* "Farm out" `(X + Y) x N` work to specialized agents, returning only the final Z token answers, keeping the main context clean
* However, custom subagents gatekeep context
  * `PythonTests` subagents have testing-related context that is hidden from the main agent
* Author prefers Claude's built-in `Task` feature to spawn clones of the main agent

## Hooks
* Hooks are deterministic "must-do" rules that complement the "should-do" suggestions in `CLAUDE.md`
* Block-at-Submit Hooks: example is a hook that checks to see if a `/tmp/agent-pre-commit-pass`  file exists
  * This file only exists when all tests pass
  * If the file is missing, the hook blocks the git commit, forcing Claude into a test and fix loop until all tests pass
* Intentional not to use block-at-write hooks as blocking an agent mid-plan confuses / frustrates it
