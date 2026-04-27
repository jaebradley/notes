# [Git Tips And Tricks](https://about.gitlab.com/blog/2016/12/08/git-tips-and-tricks/)

* Set a global `.gitignore` and add the path to the global `.gitignore` as an excluded file to the `.gitconfig`
* Set the `fetch.prune = true` to the `.gitconfig` to delete any stale branches in local repo that no longer exist in remote on every fetch and pull
* Enable autosquashing by default when squashing or fixing up commits during an interactive rebase (`rebase.autosquash = true`)
* Enable extra info when using Git submodules (`status.submoduleSummary = true`)
* Change the editor of Git's messages to whatever you'd like
  * `$GIT_EDITOR` environment variable, then `core.editor`, then `$VISUAL` environment variable, then `$EDITOR` environment variable are checked in that order
  * Set it in `core.editor` in the `.gitignore` by setting the value to whatever editor you'd want
* Change the git diff tool from the default / internal git tool by setting the `diff.tool = some-tool-name`
  * Can do something similar for the `merge.tool` setting
* `@` is an alias for `HEAD`
* use `-` to refer to the previous branch you were on, so `git checkout -` checks out the previous branch

## Delete local branches that have been merged to master

```bash
# Make sure you have checked out master first
git checkout master

# Delete merged branches to master except master
git branch --merged master | grep -v "master" | xargs -n 1 git branch -d
```

* `git remote prune origin` deletes local branches that no longer exist in the remote repo
* `git checkout -b new_branch base_branch` checks out a new branch from some base without having to check out that base branch first
