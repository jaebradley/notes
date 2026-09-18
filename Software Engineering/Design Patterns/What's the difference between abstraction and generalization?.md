# [What's the difference between abstraction and generalization?](https://stackoverflow.com/questions/19291776/whats-the-difference-between-abstraction-and-generalization)
* Abstraction reduces complexity by hiding irrelevant details
* Generalization reduces complexity by replacing multiple entities which perform similar functions with a single construct
* [Author](https://stackoverflow.com/questions/19291776/whats-the-difference-between-abstraction-and-generalization) uses a `Book` model as an example
  * Books have many properties - page count, weight, fonts, cover images - but for the specific book system that's being built, the only properties that matter are title, ISBN number, and whether the book is borrowed
  * So in this case, the `Book(title, ISBN, borrowed)` model is an abstraction of the real books in the library
* Example of generalization is `List<Generic>` vs. `StringList`, `IntList`, etc
  * The `List` hasn't been abstracted, since properties or details haven't been removed, the `List` functionality has been extended or made applicable to a wider (more generic) range of items
* Extending the library / book example - there are other items other than books that can be borrowed (they are `Borrowable`)
  * There can be _generalized_ logic to lending _any_ `Borrowable` item
