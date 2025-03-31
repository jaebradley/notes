# [AI Blindspots](https://ezyang.github.io/ai-blindspots/)

## Stop Digging
* LLM created non-deterministic tests that kept passing, then failing
* LLM started relaxing test conditions instead of sampling deterministically

## Black Box Testing
* You should test the functionality of a component without knowing its internal structure
* By default, LLMs have difficulty abiding by this as the implementation file will be in context

## Preparatory Refactoring
* Generally speaking, the principle is to first refactor to make an implementaiton change easy, and then make the change
* LLMs, without a plan that explicitly asks them to refactor first, don’t decompose changes and will try to change everything at once
* May also clean up unrelated changes while they’re making a change
* LLM is instructed not to do irrelevant refactors
* Accurately determining the span of code the LLM should edit could also help

## Stateless Tools
* Every invocation of a tool should be independent from every other invocation
* There should be no state that persists between each invocation that has to be accounted for when doing the next invocation
* Shell has a particularly pernicious form of local state - the current working directory
* LLMs have a tough time keeping track of the current working directory
* Endeavor very hard to setup the project so that all commands can be run from a single directory
* Author’s example is a TypeScript project that was divided into different workspaces and Cursor needed to cd into the appropriate workspace driectory and run commands

## Rule of Three
* LLMs may need to be explicitly asked to not duplicate code / refactor
* Once code is copy-pasted everywhere, the LLM will assume that you want to continue copy/pasting everywhere
