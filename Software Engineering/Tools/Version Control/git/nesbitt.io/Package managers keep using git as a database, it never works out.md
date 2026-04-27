# [Package managers keep using git as a database, it never works out](https://nesbitt.io/2025/12/24/package-managers-keep-using-git-as-a-database.html)

* Git-based wikis become too slow to be usable at scale
* Git-based CMS platforms like Decap hit GitHub's API rate limits
* Underlying issue is that git inherits filesystem limitations and filesystems are not optimized to be databases
* Directories with too many files become slow
  * Can be fixed using hash-based sharding by splitting directories by the first few characters of a hashed name
  * Git does this internally with its objects folder, splitting it into 256 subdirectories
* Git is case-sensitive but macOS and Windows filesystems typically aren't
* Path length limits: Windows restricts paths to 260 characters
  * Git supports longer paths but Git on Windows inherits the 260 character path limitiation
