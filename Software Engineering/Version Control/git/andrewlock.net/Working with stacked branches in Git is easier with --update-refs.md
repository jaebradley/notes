# [Working with stacked branches in Git is easier with --update-refs](https://andrewlock.net/working-with-stacked-branches-in-git-is-easier-with-update-refs/)
* The `--update-refs` option will automatically force-update any branches that point to commits that are being rebased
* Stacked rebased branches usually involve an (arduous) process of checking out the lates `main` branch, and then rebasing the feature branches on `main` / the previous feature branch
* What `--update-refs` effectively does is rebase the `nth` feature branch on `main` and then `reset` the feature branch for the set of commits that were contained in that feature branch
  * It rebases a branch and "remembers" where all the existing local branches point
* Process is checking out the "last" or most-current feature branch and then rebase the tip with all intermediate branches
  * In the author's case, the author would checkout `feature-part-3` and then `rebase` `feature-part-3` on `feature-part-1` with the `--update-refs` option
  * Still need to checkout the intermediate branches and force push them
* Scenario is working on `feature-part-3` a typo is found and needs to be included in the `feature-part-1` branch
  * `git rebase main -i --update-refs` : this is executed on the `feature-part-3` branch
  * During the interactive rebase, move the `fix typo` commit into the set of commits that are part of `feature-part-1`
* `--update-refs` can be enabled by default for all repos by adding it to the global config
