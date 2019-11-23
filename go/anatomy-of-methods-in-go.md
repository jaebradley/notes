# [Anatomy of methods in Go](https://medium.com/rungo/anatomy-of-methods-in-go-f552aaa8ac4a)

* Method is a function that belongs to a _type_
  * Requires a receiver, which is the _type_ that the function belongs to
  * Method can access properties of the receiver that it belongs to

```go
type Employee struct {
  FirstName, LastName string
}

func (e Employee) fullName() string {
  return e.FirstName + " " + e.LastName
}

func main() {
  e := Employee{
    FirstName: "Ross",
    LastName: "Geller",
  }

  fmt.Println(e.fullName())
}
```

* `fullName` is a method that takes an `Employee` type
* Can use two methods with same name in same package if the receiver is different

```go
func (r Rect) Area() float64 {
  return r.width * r.height
}

funct (c Circle) Area() float64 {
  return math.Pi * c.radius * c.radius
}
```

* Methods need to specify pointer receiver if they want to modify struct

```go
// e won't change if called with this method
func (e Employee) changeName(newName string) {
  e.name = newName
}

// need to use pointer value as receiver
func (e *Employee) changeName(newName string) {
  (*e).name = newName
}
```

* Can get pointer of `Employee` by doing something like `ep := &e`
* Then can call `changeName` on the pointer like `ep.changeName("new name")`
* The `*e` inside `changeName` dereferences the pointer, so that it points to the actual `Employee` value in memory
* However, go is smart enough that if method receiver specifies a pointer, can simply call `e.name = newName` and go will automatically dereference pointer
* Similarly, go is smart enough that you don't have to get pointer in-order to call method on pointer, even if method receiver type is a pointer
  * Simply call method on object like `e.changeName`

## Nested Struct

* Can call method on inner struct using `.`

```go
type Contact struct {
  phone, address string
}

type Employee struct {
  name string
  salary int
  contact Contact
}

func (c *Contact) changePhone(newPhone string) {
  c.phone = newPhone
}

func main() {
  e := Employee{
    ...
    contact: Contact{
      ...
    }
  }
  e.contact.changePhone("some phone number")
}
```

* If field is anonymous, then struct fields are promoted

```go
type Employee struct {
  Contact
}

func (e *Employee) changePhone(newPhone string) {
  e.phone = newPhone
}
```

* Similar situation for methods implemented on inner struct that is anonymous so if `changePhone` is modified to take a `Contact` pointer then the `changePhone` method is available on the `Employee `struct

```go
func (c *Contact) changePhone(newPhone string) {
  c.phone = newPhone
}

func main() {
  // set up employee
  e.changePhone("some new phone number")
}
```

* `Contact` field is promoted, and thus, any method on it is promoted as well
* Methods can accept pointers or values, regardless of the receiver type
  * If a method's receiver type is `func (e Employee)` it can still be called with a pointer correctly like `(&e).showSalary()`
  * If method's receiver type is the type and not a pointer, even if the method is called with a pointer, any value modifications that take place inside the method will _not_ be applied as go will send a copy of the value (defined by the pointer) to the method
