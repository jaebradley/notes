# [Stacked Diffs with `git rebase --onto`](https://dineshpandiyan.com/blog/stacked-diffs-with-rebase-onto/)
* Large feature that is split into multiple branches that are "stacked" on top of each other
* A "regular" rebase (\`git rebase main\`) replays commits on top of the target branch
* The downstream (or "stacked") branches still point to the previous parent branches commit
  * So each "stacked" branch needs to be rebased on the previous parent's branch
* `git rebase --onto <new parent> <old parent> <branch>` specifies which commits to move, from where
  * "Take everything after the `old parent`, on the specified `branch`, and replay it onto the `new parent`"

## Step-by-Step: Using `rebase --onto`
* Create a `feature-2` off of `feature-1` , create a marker branch (\`feature-2-base\`)
  * `git branch feature-2-base feature-1` 
* So when `main` updates and the feature branches need to be synced
  * `git checkout feature-1`
  * `git rebase main` (rebases `feature-1` on top of `main`)
  * `git rebase --onto feature-1 feature-2-base feature-2` 
    * Rebase `feature-2` onto the updated `feature-1` branch
  * `git branch -f feature-2-base feature-1` 
    * Update the marker branch after a successful rebase
    * This last update isn't optional, need to continue repointing the feature branch

## Once a feature branch merges
* When `feature-1` is merged into `main`, `feature-1`'s commits are no longer needed in `feature-2`'s history
* `git checkout feature-2` 
* `git rebase -i main`
* Delete / `drop` the commits from `feature-1`
* Git will reply only the `feature-2` commits directly on `main` 

## Final thoughts
* Use `git push --force-with-lease`
* Two or three levels is manageable - beyond that, the maintenance overhead outweighs the benefits
