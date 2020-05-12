# [Structures in Go](https://medium.com/rungo/structures-in-go-76377cc106a2)

* A struct type is nothing but a schema containing the blueprint of a data a structure will hold
* Declaring a struct like `var foo Foo` gives the zero value of the struct - this is because the variable `foo` of type `Foo` has been defined but not initialized
  * Zero value of struct is a struct with all fields set to their zero values
  * So `string`s will have zero value of `""`, `int`s will have `0`, etc
* Can create a pointer to a struct in one statement - `s := &StructType{...}`
  * To get value of a field from a struct pointer, need to use `*s` (dereferencing syntax) to get struct
  * Go also takes care of deferencing a pointer under the hood, so can also just do `s.Field` even if `s` is a pointer to a struct
* Anonymous nested struct fields get promoted in Go

```golang
type Salary struct {
  basic int
  insurance int
  allowance int
}

type Employee struct {
  firstName, lastName string
  Salary
}
```

* Can get `basic` field on `Employee` `Salary` by doing `employee.Salary.basic` or `employee.basic` since all non-conflicting fields get promoted
* Struct fields can be of an `interface` type
  * If a field defined as an `interface` type does not have an explicit value set, Go panics with a runtime error since the default value of an `interface` will be `nil` and will be trying to call method on `nil`
  * Avoid having struct field with an `interface` type
  * Methods are promoted when a struct field is an anonymous interface (with similar non-conflicting rules)
* Only struct fields with a starting uppercase letter are visible outside the package
* If a nested struct is anonymous, then only its fields starting with an uppercase letter will be available as a promoted field