# [Beautify Your Golang Project](https://itnext.io/beautify-your-golang-project-f795b4b453aa)

* Author's recommended project structure
  * `project`
    * `model`
    * `repository`
    * `handler`
    * `driver`
    * `main.go`
* `model` stores all the `struct`s that will be used throughout the project
* `repository` - interface with the database layer
  * Build interfaces to engage with the persistence layer
  * Then the interfaces are implemented in a `{model}_{persistence_layer}.go` file (so, `post_mysql.go`)
  * The specific persistence layer is noted in the file name in case data is moved to another persistence layer like mongo and a different implementation needs to be used to satisfy the same interface
* The `handler` directory is basically used as an interface between a request and the data layer
  * Function implementations decide _which_ `repository` implementation to call, and how to structure the data to the caller

