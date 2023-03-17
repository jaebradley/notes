# [Validation, Database Constraint, or Both](https://robots.thoughtbot.com/validation-database-constraint-or-both)

* Schema constraints ensure data persisted to the database are consistent with requirements, regardless of source (`INSERT` statement vs. model creation)
  * This includes, but is not limited to existence, uniqueness, and foreign key constraints
* Model validations are mostly for error consistency
  * If a field is missing, presence validation will add helpful error message
* Preventing bad data should *always* be accompanied with a database constraint
* Preventing recoverable user errors should have a model validation (submitting a `name` value without enough characters)
* Rails 5 added behavior around `belongs_to` associations that marks them as required
