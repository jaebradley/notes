# [Four Things About Pry](https://revs.runtime-revolution.com/four-things-about-pry-a0ebb10c6c2e)

* `REPL` stands for `R`ead `E`val `P`rint `L`oop
* `_` for previous value
* `__` for penultimate value
* `whereami` shows the full context of a `binding.pry`
* `show-source` shows the full contents of a method
* `hist` shows previous commands
  * `hist --replay 3..4` will replay the 3rd and 4th most recent commands
* `amend-line <line_number>` when typing in multi-line expressions
* `edit <SomeClass.some_method>` will edit method definition
* `reload-method <SomeClass.some_method>` will reload / evaluate method definition
* `show-model` lists the model attributes and class associations
