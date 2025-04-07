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
* LLMs love to duplicate code
* If you ask an LLM to modify a program, it will spit out a new copy of the program with the requested changes
* If code is copy-pasted everywhere in the codebase already, the LLM will assume that you want to continue copy/pasting the code everywhere
* It’s up to you to ask the model to reduce duplication

## Requirements, not Solutions
* Common antipattern when figuring out what to do is to jump straight to proposing solutions, without forcing everyone to clearly articulate what all the requirements are
* Overspecify requirements vs. underspecify requirements to the LLM
* If an LLM misunderstands you, it is best to edit the original prompt and try again
* Previous conversations stay in context and leaving the incorrect interpretation in context will make it harder for the LLM to match requirements
* If you are certain some aspects of the solution should work a particular way, tell the LLM about it
* It’s important to be right about this as the LLM will try very hard to follow what you ask it to do
* Example: ask Sonnet to make a visualization, and it will generate an SVG. Ask it to make it an interactive visualization, and it will produce a React application. Divergence based on a word.

## Use Automatic Code Formatting
* Don’t waste LLM capacity having the LLM fix lint errors

## Keep Files Small
* Large files will overflow the LLM’s context
* Author’s example is at 128KB, Sonnet 3.7 has trouble modifying the entire file
* Sonnet’s context window is only 200k tokens

## Read the Docs
* Big wins of LLMs is that they know so many things based on their training
* For extremely popular frameworks, an LLM is likely to have memorized most aspects of how to use the framework
* For knowledge that is beyond the knowledge cutoff, you will likely get a model that hallucinates things
* Ideally, an agentic model would know to do a web search and find the docs it needs
* Example: Author was trying to use the LLM to write YAML configured to call a Python function. Model hallucinated how this should work. Providing the documentation to the model was the context necessary for the model to fix the error and update the output format to match the documentation.

## Walking Skeleton
* The minimum, crappy implementation of an end-to-end system that has all the pieces you need
* Point is to get the end-to-end system working and then start improving the various pieces
* It has never been easier to get to a walking skeleton implementation using LLMs

## Use Static Types
* LLMs greatly reduces the pressure to choose a language that is good at prototyping as LLMs can write boilerplate and refactor
* Want an agent setup where the LLM is informated about type errors after changes, so the LLM can easily tell what other files they need to update when doing refactors
* LLM training corpuses emphasize Python and JavaScript

## Respect the Spec
* While occasionally the right thing to do is to change the existing API, generally speaking, respecting the existing API is desired.
* LLMs are not very good at respecting the existing API. 
* They’ll delete tests, change APIs, etc
* Some of these boundaries can be encoded in the prompt
* One of the most important functions of reviewing LLM generated code is ensuring it doesn’t change the spec in an unaligned way
* Example: Sonnet was failing to fix a test so it replaced the contents of the test with `assert True`

## Memento
* LLM has no memory of your codebase
* LLM must reconstruct enough context to execute a task
* In order to help the model gain context, make sure there is documentation it can reference, and make sure the model can find it

## Know Your Limits
* Sonnet 3.7 is not very good at knowing its limits - if you want it to tell you when it doesn’t know how to do something, at minimum, you will have to explicitly prompt it
* Examples: Sonnet 3.7 deeply, deeply believes it has the capability to execute shell commands. If there is no command available, if Sonnet decides to make a file executable, it will start creating random shell scripts. Workarounds are to improve the prompt, or give Sonnet a tool that does the thing it wants to do.

## Culture Eats Strategy
* When an LLM generates code, it will generate it with a style that is based off of how it was fine-tuned, as well as its context window
* So if a lot of the text in the context window uses a library, the LLM will continue to use that library. Conversely, if the library is not mentioned at all, and the LLM is not fine-tuned to reach for it by default, it will not use it.
* If the LLM is consistently doing things you don’t like, you need to change its “culture”
* This could be adding Cursor rules, which modifies the prompt
* It could also involve refactoring the existing code to follow the style you want, since LLMs are trained to predict the next token in context
