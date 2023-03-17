# [`An opinionated guide to readable RSpec`](https://medium.com/selleo/an-opinionated-guide-to-readable-rspec-part-1-of-2-fe1dce79a478)

* This means we should drop all usages of `let`, `subject`, and `described_class` and limit usages of `before`, `around`, and `after`
* Code will be duplicated but while reading the test, the developer will be fully aware of the test context
* `describe` is either a feature or class name
  * prepend a `.` when testing class-level methods (`.send_notifications`)
  * prepend a `#` when testing instance methods (`#send_notifications`)
  * prepend a `:` when testing specific parts of a hash (`:data`)
  * use uppercase when testing constants
  * use actual class names when describing classes
* A good practice is to start `context` descriptions with `when`
* Each `context` should describe only one aspect of the environment
  * `when a user is an admin and the day is Sunday` should be split into two `context`s
* `it` block should use present tense and keep expectations concise, precise, and unambiguous
  * If expectation descriptions are too long, it means that tests should be split up
