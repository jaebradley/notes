# [Search A Git Repo Like A Ninja](https://travisjeffery.com/b/2012/02/search-a-git-repo-like-a-ninja/0

* Allow Perl regular expression - need to install `git` with the `--with-pcre` flag and then `git config --global grep.extendRegexp true`
* Always include line numbers: `git config --global grep.lineNumber true`
* Group output like `ack`: `git config --global alias.g "grep --break --heading --line-number"
  * With this alias, can do something like `git g`
* Search in another branch like `git grep -e <some regex> some_other_branch -- '*.js'`
  * Searches for regex in another branch across JavaScript file
  * `--` signals end of options
* Can use the `--cached` argument to search files registered in the index vs. working tree
* Search through previous revisions whose contest contain a given regexp
  * `git grep <some regex> $(git rev-list --all)`
* Search for commits whose changes include regexp
  * `git log -G <some regex>`
* Can combine regexps like `git grep -e 'include' --or 'extend' --and \( -e 'Specification' -e 'Factory' \)
  * Search for lines which have "include" or "extend" and "Specification" or "Factory"
* Find files that contain some terms, not necessarily on the same line - `git grep --all-matches -e <some regex> -e <some other regexp>`
* Find commits whose message mention login and were authored by jae in the last month - `git log --grep=login --author=jae --since=1.month`
