# [How to Write Computer Programs](https://www.dyalog.com/uploads/documents/Papers/declarative_prog.pdf)

## A Parable

### Dewy

* Uses temporal words when thinking about programming “First do this, then do that”
* Says “`N` gets one”
*  Refers to `N` as a *variable*
* Assignment is right-to-left - a value is “put” into a named pigeonhole
* Comments code with injuctions e.g. “initialize result”
* Plus, minus, times, divide (Dewy) vs. sum, difference, product, quotient (Izzy)

### Izzy

* Does not use temporal words when thinking about programming
* Says “`N` is one”
* Refers to `N` as a *definition*
* Assignment is a name *indicating* an existing value to its right
* Comments code using descriptive noun phrases e.g. “Mean value”
* Describe the result in terms of the arguments, using only the present tense of the verb “to be”


* Declarative languages (in particular, functional languages) are generally accepted to be easier to program, produce more stable code vs. procedural languages
* Do-y = procedural language in parable
* Is-y = declarative language in parable

* One significant downside of `is-y` programs is that they run slower than corresponding `do-y` programs
* Indexed assignment is significantly quicker than a function that meshes together parts of an array to form a third array

## A note on the conditional construct

* Conditional execution has three parts in both procedural and declarative languages
* `test`, `true`, and `false` parts
* In procedural languages,  the `true` and `false` parts are non-result returning (void) `do-y` statements
* In declarative langauges, the `true` and `false` parts are result returning expressions
* The procedural `true` and `false` parts are clauses
* “If it’s raining, put on a coat, else wear a jacket”
* The declarative language equivalents are *noun phrases*
* “I would like: if you have milk, then tea, else coffee”
* In a procedural language, the `false` part may be omitted
* “If it’s raining, put on a coat”
* In a declarative language, such an omission would render the “sentence” incomplete

## A short note on the Declarative Programming Style

* Example is finding the arithmetic mean of an array
* Functional definition - “arithmetic mean IS the quotient of the sum and number of its items”
* Procedural equivalent, implemented with loop has temporal language
* First, do this, then do that, repeat until, etc
* “Destructive” verbs like “zero out”, “set”, “increment”, “decrement”
* End up with statements like “index is one less than index”
* Such words are indicative of a program having state
