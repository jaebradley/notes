# Business Logic

## [What is business logic](https://www.reddit.com/r/learnprogramming/comments/17ax07l/what_is_business_logic/)
* Example: logic to determine if person X is eligible for a tax deduction
* The rules that determine if this person is eligible or not - that’s business logic
* This logic isn’t logic to sort an array, for example

## [What constitutes business logic](https://www.reddit.com/r/learnprogramming/comments/17188t1/what_constitutes_business_logic_and_what/)
* Typically, keep data layer as lean as possible
* Requests data from the data source, retrieves the data, maps it into a usable object

## [What is business logic](https://www.reddit.com/r/SoftwareEngineering/comments/fdwu2v/what_is_business_logic/)
* Small family hotel example
* Needs to know - number of rooms, how price is calculated, discounts, accounting, etc (business rules)
* Family looks for software that implements these business rules
* How the data is stored (what type of database) or presented (mobile app, web-based?) are implementation details
* Business rules = policy, mechanism = implementation details

## [What really is business logic](https://softwareengineering.stackexchange.com/questions/234251/what-really-is-the-business-logic)
* If you were to reimplement a software project in a different programming language, business rules/logic is what the old and new projects would have in common
* Programming language, source code, tools, user interface, documentation would all be different - but the underlying logical constraints would be the same
* Example is a business rule around an invoice having one line item
  * Whether using a GUI or magnetic tape of an SSD (so some parts of the underlying technology), these solutions would all match the one line item rule

## [How to organize business logic which deals with different, but related objects](https://softwareengineering.stackexchange.com/questions/371866/how-to-organize-business-logic-which-deals-with-different-but-related-objects)
* Data holder = collection of facts
* Agent object is a `string EmployeeID`, a `string Name`, and an `int NumberOfCalls`
* Just because data holders don’t seemingly do anything doesn’t mean that they don’t contain business logic
* Agent might require all fields to exist, which is now a business rule
* If you cannot change an employee ID for an agent after the agent has been created, that’s another business rule
* "True" OOP = Objects do **only* *one thing
* `CallRegisterer` has a public method `RegisterCall(Call, Agent)` and it depends on an `AgentsRegistry` and a `CallQueueRegistry`
