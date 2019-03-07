# [What is a bare git repository?](http://www.saintsjd.com/2011/01/what-is-a-bare-git-repository/)

* Repositories created using `git init` are working directories
  * they have a `.git` subfolder with all revision history
  * a working tree / checked out copies of project files
  * `git clone` will also create a working directory
* Base repositories (`git init --bare`) are sharing directories
  * Don't contain checked out copies of source files
  * Store git revision history in root directory instead of in a `.git` subfolder
  * Generally have a `.git` extension
  * This is effectively what a `GitHub` repo is
    * Repo is cloned to local working directory
    * Changes are made and then pushed to (bare) `GitHub` repo
    * These changes are now available to other users - but no files in the repo itself is directly edited
