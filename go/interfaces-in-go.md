# [Interfaces In Go](https://medium.com/rungo/interfaces-in-go-ab1601159b3a)

* Primary job of an interface is to provide only method signatures consisting of the method name, input arguments, and return types

```golang
type Shape interface {
  Area() float64
  Perimter() float64
}

var s Shape
```

* The static type of an interface is the interface definition itself (`Shape`)
* The dynamic value of an interface is a type that implements the interface
  * For example, the variable `s` could reference any value that implements the `Shape` interface

```golang
type Rect struct {
  width float64
  height float64
}

func (r Rect) Area() float64 {
  // impl 
}

func (r Rect) Perimeter() float64 {
  // impl
}

var s Shape
s = Rect{...}
```

* `Rect` implements the `Shape` interface, but there's no explicit interface usage declaration - interfaces in Go are implicitly implemented
  * Golang will still raise a compilation error if an interface definition is not completely satisfied by a struct
* Note how `s` can be reassigned to reference the `Rect` struct since `Rect` implements the `Shape` interface - similarly, `s` can be reassigned to any struct that implements the `Shape` interface
* An empty interface is `interface{}`
  * All types implement this interface implicitly
  * This is how the `Println` function can accept different types of values - the signature is `func Println(a ...interface{}) (n int, err error)`
* Type assertion is used to extract the dynamic type from an interface type
  * If there's a `Cube` struct that implements the `Shape` interface

```golang
var s Shape = Cube{...}
c := s.(Cube)
// Can call Cube methods on c that don't necessarily exist for Shape interface
```

* An interface cannot implement other interfaces or extend them, but a new interface can be created by merging two or more interfaces

```golang
type Shape interface {
  Area() float64
}

type Object interface {
  Volume() float64
}

type Material interface {
  Shape
  Object
}
```

* Anything that implements the `Material` interface also implements the `Shape` and `Object` interfaces
* Like anonymously nested structs, all methods of nested interfaces get promoted to parent interfaces

```golang
type Rect struct {
  width float64
  height float64
}

func (r *Rect) Area() float64 {
  // impl
}

func (r Rect) Perimeter() float64 {
  // impl 
}

// Compilation error
var s Shape = Rect{...}
```

* The compilation error is because `Rect` does not implement the `Area` method but that `*Rect` does
* Need to have `var s Shape = &Rect{...}`