# [Git log customization](https://justinjoyce.dev/customizing-git-log-format/)
```gitconfig
[alias]
	lg = log --graph -10 --format='%C(yellow)%h%Creset  %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' 
```
* Get the last 10 commits
  * This can be overridden like `git lg -N`
* Use the `graph` display option
* The formatting
  * Prints the commit hash in yellow
  * Prints the commit message in the default color
  * Prints the parenthesized relative date of the commit in green
  * Prints the bracketed author's name in bold and blue
