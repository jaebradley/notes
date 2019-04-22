# How Git thinks about merges

* Most VCS treat the operation of merging branch `A` into branch `B` as two separate operations
  * A commit is either added to branch `A` with all the changes from branch `B` or vice versa
  * Git designers noticed that both approaches led to the same outcome - i.e. the same set of files
  * Think of either case as "merge all changes from branch A and branch B into single branch"
* Squash merges
  * Takes individual commits in a branch and "squashes" them into a single commit
  * The side-effect is that the entire history of this squashed branch is lost
  * Benefit is that it makes history a lot easier to reason about (like when using `bisect`)

