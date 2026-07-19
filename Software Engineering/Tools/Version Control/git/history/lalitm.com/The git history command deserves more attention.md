# [The git history command deserves more attention](https://lalitm.com/post/git-history/)

## `fixup`
* `git history fixup` fixes an old commit, and then autorebases _all_ other branches that contain this commit to match
  * `git rebase --update-refs` only moves refs sitting inside the range that is being actively rebased
* Does not work well with merge commits
* The operation is atomic, and refuses any operation that could produce a conflict across all rebased branches

## `reword`
* `git history reword` updates the commit message of an old commit and automatically rebases everything on top of the updated commit

## `split`
* `git history split` splits a commit into two, letting the author pick which hunks to keep in the original commit's diff
  * Equivalent to `git add -p` without needing to explicitly execute `git rebase` afterwards
* Any downstream commits of the split commit are rebuilt on top of the new (split) commit history
