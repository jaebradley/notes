# [A Practical Guide to fzf: Building a File Explorer](https://thevaluable.dev/practical-guide-fzf-example/)

## Default Behavior
* Uses the `find` program to get files and subdirectories of the working directory
  * Hidden files are skipped
* This default command can be overwritten via the `FZF_DEFAULT_COMMAND` environment variable

```
# Use the CLI find to get all files, excluding any filepath
# containing the string "git".
export FZF_DEFAULT_COMMAND='find . -type f ! -path "*git*"'

# Use the CLI fd to respect ignore files (like '.gitignore'),
# display hidden files, and exclude the '.git' directory.
export FZF_DEFAULT_COMMAND='fd . --hidden --exclude ".git"'

# Use the CLI ripgrep to respect ignore files (like '.gitignore'),
# display hidden files, and exclude the '.git' directory.
export FZF_DEFAULT_COMMAND='rg --files --hidden --glob "!.git"'
```

## Search Syntax
* Can use regex to match results
  * For example, `^./some beginning file path`  will match file paths that begin with `./some beginning file path` 
* There is also an "or" operator like `.xml$ | .yml$ | .txt$` which will match all files that have an `xml`, `yml`, or `txt` extension
* And-ed filters are separated by spaces
  * So `.txt$ /jae/` would match files with a `txt` extension that contain `jae` in the file path
* Exact matching uses the `-e` option when executing `fzf` or a single quote when specifying a query
