# `git diff`

* Original file is flagged by `---`
* New file is flagged by `+++`
* `@@` provides line number context for both file versions
  * `-` indicates a line that was removed while `+` indicates a line that was added
* You can diff anything between
  * Any tree object within entire commit graph
  * Working directory
  * Index
* By default, `git diff` will show diff between working directory and index
  * i.e. what is "dirty" in working directory and what is a candidate to stage in next commit
  * Does not diff things in index and what's in repo
* `git diff --cached <commit>`
  * Will show difference between staged changes in index and the specified commit
  * Often times, `HEAD` is used as the commit to see how a commit would impact current branch
  * Can use alias `--staged`
* `--M`
  * Detects file renames and will generate simplified output rather than showing full addition / deletion
  * If there are content changes and a rename, then it will show output
* `--stat` shows statistics about change
* `git diff` doesn't care about file history
* Can specify a path to get diffs for that path (or even a single file) - `git diff <PATH>`
* Can also use "pickaxe" / `-S<search_term>` option like `git diff -S"foobar" master~50` to search for `foobar` across past 50 commits
