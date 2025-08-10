# [Writing Your Own Simple Tab-Completions for Bash and Zsh](https://mill-build.org/blog/14-bash-zsh-completion.html)

## Basic Tab Completion
* Basic tab completion in Bash/Zsh is registering a handler function that is called when `tab` is pressed
* Handler is given the currently written words, and the index of the word that the user's cursor is over
* There are shell-specific completion APIs
  * Bash completion functions need to set the `COMPREPLY` environment variable
  * Zsh completion functions need to call `compadd`
  * These shell completion functions interact with a generalized function that generates the completions for your application

## Zsh Completion Descriptions
* Bash does not support completion descriptions by default, so we need to trim off the descriptions
* In Zsh, pass the raw words + descriptions as well as the "trimmed" words + descriptions to `compadd`
 * Trimmed words are based on current input
* Zsh only displays the `raw` lines that contain both the completion word + relevant descriptions
* Zsh uses the "trimmed" lines when completing the actual command

## Hacking Bash Completion Descriptions
* Completions are generated dynamically
* Bash (and zsh) only insert text that is a common prefix to all completion options
* In Bash, end up appending description text to the right of the matching completion option

## Showing Single-Completion Descriptions
* Completions when tabbing on a complete word/option
* Check if the token is a complete matching word
 * If so, add a second "dummy" completion
 * Causes Bash and Zsh to print out completions and descriptions
 * This "dummy" completion strategy involves _always_ adding the untrimmed lines containing the completion descriptions
 * If there's exactly one matching line, add an additional word-only completion; description is trimmed off
