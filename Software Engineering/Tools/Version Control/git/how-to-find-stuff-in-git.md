# [How To Find Stuff In Git](https://medium.com/@tygertec/how-to-find-stuff-in-git-35d4cb8c1845)

* `git log --patch foo.c` - shows what changes were associated with a given file on a commit-by-commit basis
* `git log -S "some search string"`
* `git bisect start` -> `git bisect bad HEAD` -> `git bisect good <commit hash>`
  * `bisect` will select a commit in the range defined and then will check it out for you
  * Check if the bug still exists, and if it doesn't then `git bisect good`
  * If it does, then `git bisect bad`
  * This "slicing" of the set of commits should whittle down the possible commits that introduced the bug
  * `git bisect reset` returns to the `HEAD`
  * `git bisect start HEAD <good_hash> run ./build_script.sh` will automatically find the bad commit for you
* `git log --since=two.weeks.ago --reverse -- foo.c`
  * The `--` tells Git it shouldn't expect any more switches / flags
* `git shortlog -s -n` - shows who committed the most times int he whole repo
* `git grep` supports displaying the file headings (`--heading`) and line numbers (`--line-number`) for matches
* `git log --after="April 1, 2019" --before="September 1, 2020"` - shows commit in a date range
* `git log -L1,10:foo.c` - show commits where certain lines in the file was changed
  * `git log -L:bar:foo.c` - shows when a certain method in a class was changed
