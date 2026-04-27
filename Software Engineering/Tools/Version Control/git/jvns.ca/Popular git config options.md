# [**Popular git config options**](https://jvns.ca/blog/2024/02/16/popular-git-config-options/)

## `merge.conflictstyle zdiff3`
* `diff3` adds the original version of the code in the middle of the diff so

## `init.defaultBranch main` 
* `main` branch is a better name than `master` 

## `commit.verbose true` 
* Adds the whole commit diff in the text editor when writing the commit message
* Helps to write a commit message that reflects what was accomplished in the commit

## `help.autocorrect 10`
* By default, git's autocorrect tries to check for typos, but won't run the corrected command
* `prompt` will show a yes/no prompt to execute the corrected prompt

## `core.pager delta` 
* `pager` is what git uses to display the output of `git diff/log/show`
* `delta` is a diff viewing tool with syntax highlighting
  * `delta` also suggests setting up `interactive.diffFilter delta --color-only` to add syntax highlighting when executing `git add -p`

## `diff.algorithm histogram`
* People aren't a fan of git's default diffing algorithm
* Author finds the histogram diffing algorithm cleaning

## `core.excludesfile: a global .gitignore`
* `core.excludeFiles = ~/.gitignore` lets you set a global gitignore file that applies to all repositories, for things like `.idea` or `.DS_Store` that you never want to commit to any repo. It defaults to `~/.config/git/ignore`.

## Replacing `https://github.com` with `git@github.com`
```
[url "git@github.com:"]
	insteadOf = "https://github.com/"
```

## Other Options
* `branch.sort -committerdate`, makes `git branch` sort by most recently used branches instead of alphabetical, to make it easier to find branches. `tag.sort taggerdate` is similar for tags.
* `credential.helper osxkeychain` to use the Mac keychain for git credential management
* `diff.context 10`: include more lines of context in diffs
* `fetch.prune true` and `fetch.prunetags` - automatically delete remote tracking branches that have been deleted
* `gpg.format ssh`: allow you to sign commits with SSH keys
