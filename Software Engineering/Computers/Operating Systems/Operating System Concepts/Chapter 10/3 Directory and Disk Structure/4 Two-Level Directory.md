# 10.3.4 Two-Level Directory

* Each user has their own user file directory
* Master file directory that is indexed by user name or account number
  * Each entry in the MFD points to the UFD of that user
* When a user refers to a particular file, only their UFD is searched
  * Multiple users may have files with the same name
  * When the operating system creates / deletes a file for a specific user, only the user's UFD is searched
* Each user is isolated from all other users, and does not allow users to cooperate and access each other's files
* If cooperation can be enabled, then the directory structure is a tree, where the MFD is the root, and all the UFDs are the direct descendants of the root
  * So a user name + a file name defines a path in this (simplistic) tree from the root (the MFD)
  * To access the `test` file of `userB`, the path would be `/userB/test`
* Programs provided as part of the system (like utility commands) are generally defined as files
  * Instead of having to copy each program file into each UFD, use a special user directory to contain system files
  * UFD is first searched, then if a file is not found, the system automatically searches the special user directory that contains the system files
  * The sequence of directories searched is called the search path, and can be extended
