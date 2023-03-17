# [Everything You Need To Know About Packages In Go](https://medium.com/rungo/everything-you-need-to-know-about-packages-in-go-b8bac62b74cc)

```bash
* bin
* src
  * app
    - entry.go
```

* `app` is the package since `app` is child directory of `src`
* So `go install app` will look for `app` sub-directory inside `src` directory of `GOPATH` since `GOROOT` doesn't have it)
  * Then it compiles package, creates a binary executable file with the same name as the package name inside the `bin` (set by `GOBIN`) so that the binary is executable from terminal since `bin` directory is in `PATH`
  * `go install <package>` looks for any file with `main` package declaration inside specified `package` directory
    * If it finds a file, Go treats it as an executable program and creates a binary file
    * Package can have many files, but only a single file with a `main` function, since that file will be the entry point for execution
* Package names with under scores, hypens, or mixed capitalization should be avoided
* Go exports variables if the variable name starts with uppercase
* All variables not starting with an uppercase letter are private to the package
* Unlike `index.js` in Node, Go packages do not have entry file naming system - only need the entry file to implement `main` function
* If importing `greet` package, to access exported members from package, use dot notation like `greet.SomeThing`
* When importing a package, Go creates a global variable using the package declaration of the package
  * Can use a package alias when importing if there are collisions in naming

```golang
import (
  example "some/thing"
)

example.SomeExport
```

* Can nest packages since a package is just a directory
  * So if there's `greet` and then `de` package under `greet`, then import will be `greet/de`
* Package scope is top-most block of any file in the package where a declared variable is accessible from within the package (i.e. across all files in the package)

```golang
# version.go

package main

var version = "blahblahblah"

# entry.go

package main

import "fmt"

func main() {
  # Prints version ===> blahblahblah
  fmt.Println("version ===> ", version)
}
```

* Can use variable `version` even though it is _not_exported like `Version` because it is declared in package scope
* Not allowed to redeclare global variable with same name in same package (i.e. once `version` is declared, it cannot be redeclared in package scope)
* An imported package is initialized only once per package
  * So multiple import statements for an imported package is going to lead to initialization only once
