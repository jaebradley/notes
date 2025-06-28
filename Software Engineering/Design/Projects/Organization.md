# Organization

## [When designing a directory structure should filenames include folder names?](https://softwareengineering.stackexchange.com/questions/312533/when-designing-a-directory-structure-should-filenames-include-folder-names)
* `house-1/windows.txt` and `house-2/windows.txt` vs. `house-1/house-1-windows.txt` and `house-2/house-2-windows.txt`
* REST service doesn’t have redundancies within a path name
* Clear and unambiguous (for future maintenance)
* Answer dislikes deeploy nested file name like `usa.newyorkstate.newyork.manhattan.32ndstreet.218a.suite34.doors.txt`

## [How should I organize my source tree?](https://softwareengineering.stackexchange.com/questions/81899/how-should-i-organize-my-source-tree)
* A well-structured architecture can lead to a well-structured source tree layout
  * Source tree should reflect the architecture
* `module/implementation`, `module/documentation`, `module/tests`
* `/ide` for project files
`src/storage` for specific data storage logic
`src/repositories` for data wrapping logic around specific storage implementations
`src/services` for applicaton business logic
`src/helpers` for utility classes (author used example of string manipulation)

