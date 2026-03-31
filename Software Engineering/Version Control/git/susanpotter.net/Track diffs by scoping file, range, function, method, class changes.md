# [Track diffs by scoping file, range, function, method, class changes](https://www.susanpotter.net/software/track-diffs-by-scoping-file-range-function-method-class-changes/)
* Scope diffs in an inclusive line range via `git log -L start line,end line:filename`
* Can also try and scope diffs associated with a function name like `-L :function name:file name`
  * Author's example: `git log -L :main:ext/nkf/nkf-utf8/nkf.c`
* `.gitattributes` file can can help define the language for certain file name formats
  * These language definitions can help define code blocks when diffing
  * In the author's example, the `.gitattributes` file has the following definition `*.rb diff=ruby` that helps determine the block boundaries for ruby files
