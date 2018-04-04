# File Management And The Index

* Index is between working directory and repository to stage / collect alterations
  * A set of intended or prospective modifications
* Commit is two-step - stage changes and commit changes.
  * Alteration found in working directory but not in the index isn't staged and isn't committed

## File Classifications

* Tracked
  * A file already in the repository or any file that is staged in the index
* Ignored
  * An ignored file must be explicitly declared "invisible" or "ignored" - these include compiler output and temporary / scratch files
* Untracked
  * Any file not found in either category

* The entirety of each file, at the moment you issued git add, was copied into the object store and indexed by its resulting SHA1 name - this is why staging a file is also called caching a file or putting a file into the index
* When Git executes git commit --all it recursively traverses the entire repository and stages all known, modified files, and commits those.

## Using git rm

* git rm removes a file from both the repository and the working directory
* Git will remove a file only from the index or from the index and working directory
* Removing a files from your directory and the index does not remove the file's history from the repository
* git rm --cached removes the file from the index and leaves it in the working directory
* Before Git removes a file, it checks to make sure the version of the file in the working directory matches the latest version in the current branch (the version that Git commands call the HEAD).
  * This verification precludes the accidental loss of any changes (due to editing)
  * Can get by this by using git rm -f to force the removal of your file

## Using git mv

These are equivalent

```bash
mv stuff newstuff
git rm stuff
git add newstuff
```

```bash
git mv stuff newstuff
```

* In both cases, Git removes the pathname stuff from the index, adds the new pathname newstuff, keeps the original content from stuff in the object store, and reassociates that content with the pathname newstuff.
* By using the --follow option, Git traces back through the log and finds the whole history associated with the content

## The .gitignore File

Precedence

* Patterns specified on the command line
* Patterns read from .gitignore in the same directory
* Patterns in parent directories, proceeding upwards. Hence, the current directory's patterns overrule the parents' patterns, and parents close to the current directory take precedence over higher parents
* Patterns from the .git/info/exclude file
* Patterns from the file specified by the configuration variable core.excludefile
