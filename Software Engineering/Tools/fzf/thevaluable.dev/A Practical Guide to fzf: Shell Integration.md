# [**A Practical Guide to fzf: Shell Integration**](https://thevaluable.dev/fzf-shell-integration/)

## Managing your Own Completion
* The first way to create our own custom completion is to simply create a new function named `_fzf_complete_<cmd_name>`
  * `_fzf_complete_git` would be a completion for the "git" command
  * And then `git **` would execute this function

## fzf and tmux
* Can force `fzf` to open in a new pane of the tmux session
* Use a script wrapping fzf, called `fzt-tmux`

## Customizing the Completion
* `FZF_COMPLETION_DIR_COMMANDS` specifies commands which should only be completed with directories
  * For example: `export FZF_COMPLETION_DIR_COMMANDS="cd pushd rmdir tree"`
