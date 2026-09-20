# [What is a Harness?](https://earendil.com/posts/what-is-a-harness/)
* Harnesses generally do four things
  1. Define a system prompt - set of instructions that govern how the model responds
  2. Describe and provide a tool set for the AI model to utilize
  3. Establish a "framework" for how the model behaves, like an agentic loop
  4. Provide a translation layer that enables the harness to work with a variety of different AI models
* System prompts are injected into every prompt
* Example tools are a web search tool, an email authoring tool, etc

## Agentic Loops
* Agent processes a prompt
* Uses weights to understand prompt ("what is a primary school")
* Agent will construct web search queries to fetch data
* In a harness, the AI model reviews the fetched data in the context of the initial prompt
* If the AI model determines the right information was not fetched, it can decide to search again
* This decision to call tools again is an example of "looping" behavior
* Let's say the initial prompt asked to generate a spreadsheet
* The AI model makes a spreadsheet using the "write code" tool
* The AI model compares the spreadsheet to the original prompt - if the spreadsheet data does not match the requirements of the original prompt, it may "loop" and restart fetching data
* Let's say the final requirement of the prompt was to deliver this spreadsheet via email
* The AI model calls the email composer tool, that authors an email, including attachments, like a spreadsheet
* The AI model reviews this work and decides that the prompt has been satisfied and the agentic loop closes

## Translation Layer
* Different AI models may excel at different tasks
* The translation layer allows a harness to work with different AI models
* Translation layer also lets the end user decide what model(s) to use, like an open-weight AI model

