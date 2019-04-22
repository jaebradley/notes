# Git Cherry-Pick

* `git cherry-pick <commit>` will apply the named commit to the current branch
* It generates a **new commit**
* Cherry picking doesn't _alter_ history - it just adds to the existing history

```bash
// State after cherry picking F onto feature branch
// Note that only changes related to F are moved
// No following changes are moved
A - B - C - D - E - F - Z
     \
      G - H - J - K - F'
```
