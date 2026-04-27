# Relative Commit Names

* `^` is used to signify direct ancestor / parent commits
  * `master^` refers to penultimate commit on `master` - it's the same as `master^1`
  * If a commit has multiple parents it must be the result of a `merge`
  * `branch^1` refers to first parent, `branch^2` refers to the second, etc.
* `~` is used to go back generations
  * `branch~1` is first parent, `branch~2` is first grandparent (so first parent's first parent), etc.
  * `branch~` is same as `branch~1`
* `git show-branch --more=35` (probably want to pipe to `tail -10` too) can be used to inspect historical commit graph
* `git rev-parse` is used to convert any form of a commit name (tag, relative, shortened or absolute) into a commit hash id that's in the object database

```bash
# Example git show-branch output from this repo
+++++* [master~132] double equals vs. triple equals (#130)
+++++* [master~133] Values, types, and operators (#129)
+++++* [master~134] why cant I delete my branch after squash merge (#128)
+++++* [master~135] how to misuse sqls from clause (#127)
+++++* [master~136] the little avl tree that could (#126)
```
