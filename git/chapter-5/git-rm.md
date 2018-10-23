# `git rm`

* `git rm` removes a file from the index, or from the index and the working directory
  * `--cached` only removes the file from the index
* Removing file from directory and index does not remove file's history from repository
* Before `git` removes file, it checks to see if version of file in working directory matches latest version in current branch
* Can use `git rm <filename>` to remove a file once it's been committed
