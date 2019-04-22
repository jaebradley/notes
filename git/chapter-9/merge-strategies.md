# Merge Strategies

## Degenerate Merges

* These "merges" do not introduce any new merge commits
* Already up-to-date
  * When all commits from other branch (i.e. it's `HEAD`) are already present on your branch
  * No new commit are added to branch
  * On branch `master` and then `git merge other-branch` for a second time
* Fast-forward
  * When your branch `HEAD` is fully present and represented in other branch
  * Since your branch `HEAD` is fully present, Git tacks on new commits from the other branch and moves your `HEAD` to point to the new last commit

## Normal Merges

* Resolve
  * Operates on only two branches
  * Locates direct ancestor as merge basis and performs direct _three-way merge_ 
  * Three-way merge involves applying changes from merge base to tip of other branch's `HEAD` to current branch
* Recursive
  * Operates on only two branches
  * Supposed to handle scenario where there are multiple shared merge bases between branches
  * Git merges all common merge bases
  * Uses common merged base to apply three-way merge
  * In previous case of Alice, Bob, and Cal, the recursive process would look something like
    * Start with most recent revision of Cal that Alice and Bob _both_ have
    * Calculate diff between that revision and most recent revision that Alice merged from Bob and patch those changes in
    * Calculate diff between current state and Bob's latest version and patch that in

## Specialty Merges

* `ours`
  * Only uses files from current branch
  
