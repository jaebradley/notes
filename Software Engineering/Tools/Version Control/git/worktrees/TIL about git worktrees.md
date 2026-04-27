# [TIL about git worktrees](https://www.reddit.com/r/ProgrammerTIL/comments/mtjg0c/git_til_about_git_worktrees/)
* Git worktrees let you have multiple copies of the same repository checked out
* Node modules, git submodules will need to be reinstalled for each worktree (or shared)
  * Creates isolated environments, which is preferable
* Benefit over simple branches
  * Worktree is like cloning repo into another directory, except
    * Worktree and originally cloned repo share git object store, so not using extra space
    * Cannot checkout same branch in worktree and originally cloned repository
  * Worktrees allow multiple branches checked out from a common repo, at the same time
    * Workflow example
      * Commit change to `main` 
      * `cd` to `../stable` worktree
      * `git cherry-pick` from `main` to backport the commit to `stable` 
      * Can do this even if `main` is "dirty" from other work that is in-progress
    * Interruptions don't necessarily involve stashing work or "ugly" WIP commits in branches before checking out another branch
