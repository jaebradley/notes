# [Writing Your Own Simple Tab-Completions for Bash and Zsh](https://mill-build.org/blog/14-bash-zsh-completion.html)

## Basic Tab Completion
* Basic tab completion in Bash/Zsh is registering a handler function that is called when `tab` is pressed
* Handler is given the currently written words, and the index of the word that the user's cursor is over
* There are shell-specific completion APIs
  * Bash completion functions need to set the `COMPREPLY` environment variable
  * Zsh completion functions need to call `compadd`
  * These shell completion functions interact with a generalized function that generates the completions for your application

## Zsh Completion Descriptions
