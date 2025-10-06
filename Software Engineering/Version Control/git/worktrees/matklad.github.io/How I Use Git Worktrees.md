# [How I Use Git Worktrees](https://matklad.github.io/2024/07/25/git-worktrees.html)
* Worktree for different types of concurrent activities
  * `main` worktree for looking at the latest code / what is in production
  * `work` worktree is for development
  * `review` worktree is for checking out code for PR review
  * `scratch` workflow is for creating PRs while reviewing another PR with typo fixes prepped / other fixes prepped
* Author's workflow is something like the following
```bash
# go to the `work` worktree
$ cd ~/projects/tigerbeetle/work
# Create a new branch. As we work with a centralized repo,
# rather than personal forks, I tend to prefix my branch names
# with `matklad/`
$ git switch -c matklad/awesome-feature
# Start with a reasonably clean slate.
# In reality, I have yet another script to start a branch off
# fresh from the main remote, but this reset is a good enough approximation.
$ git reset --hard origin/main
# For more complicated features, I start with an empty commit
# and write the commit message _first_, before starting the work.
# That's a good way to collect your thoughts and discover dead
# ends more gracefully than hitting a brick wall coding at 80 WPM.
$ git commit --allow-empty
# Hack furiously writing throughway code.
$ code .
# At this point, I have something that I hope works
# but would be embarrassed to share with anyone!
# So that's the good place to kick off fuzzing.
# First, I commit everything so far.
# Remember, I have `ggc` one liner for this:
$ git add . && git commit -m.
# Now I go to my `fuzz` worktree and kick off fuzzing.
# I usually split screen here.
# On the left, I copy the current commit hash.
# On the right, I switch to the fuzzing worktree,
# switch to the copied commit, and start fuzzing:
$ git add . && git commit -m.  |
$ git rev-parse HEAD | ctrlc   | $ cd ../fuzz
$                              | $ git switch -d $(ctrlv)
$                              | $ ./zig/zig build fuzz
$                              |
# While the fuzzer hums on the right, I continue to furiously refactor
# the code on the left and hammer my empty commit with a wishful
# thinking message and my messy code commit with `.` message into
# a semblance of clean git history
$ code .
$ magit-goes-brrrrr
# At this point, in the work tree, I am happy with both the code
# and the Git history, so, if the fuzzer on the right is happy,
# a PR is opened!
$                              |
$ git push --force-with-lease  | $ ./zig/zig build fuzz
$ gh pr create --web           | # Still hasn't failed
$                              |
```
