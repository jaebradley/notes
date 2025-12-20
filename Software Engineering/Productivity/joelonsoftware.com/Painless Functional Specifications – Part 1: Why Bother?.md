# [Painless Functional Specifications – Part 1: Why Bother?](https://www.joelonsoftware.com/2000/10/02/painless-functional-specifications-part-1-why-bother/)
* The most important function of a spec - describing how the program will work in minute detail - is to design the program
* Author uses an example of a "speedy" programmer who doesn't write specs and a "slow" programmer who doesn't implement logic until a spec is identified
* Project is to provide backwards compatibility for some V2 of a product
* Speedy writes a converter that converts V1 files to V2 files
  * After 2 weeks, Speedy has implemented a converter
  * This converter will force all of the customers to upgrade to the new version of the files
  * Customers are unhappy, so Speedy needs to implement a solution where the V2 version of the product will work with V1 formatted files
  * Total time is 4 weeks
* Slow spends 20 minutes designing the backwards compatibility feature and creates a spec that says "when opening a file created with an older version of the product, the file is converted to the new format"
  * Slow shows this spec to the customer and the customer provides the same feedback - we don't want to switch everybody at once
  * Slow amends the spec to say "when opening a file created with an older version of the product, the file is converted to the new format in memory. When saving the file, the user is given the option to convert it back"
  * Slow's boss looks at the spec and suggests using a V2 and V1 interface, where V2 inherits from V1's interface
  * After the feedback, Slow estimates this will take 3 weeks for a total time of 3 weeks and an hour
* A programmer who has spent 2 weeks writing some code is going to be quite attached to that code, no matter how wrong it is
  * "the best designed logic we could get, given that we'd already written all this code and we just didn't want to throw it away" vs. "the best design we could get, period"
* When you write a spec, you only have to communicate how the program is supposed to work once
  * Everybody can read the spec
    * QA can read it so they know how the program is supposed to work and they know what to test
    * Developers read it so they know what code to write
    * Customers can read it to make sure the developers are building a produc tthat they would want to pay for
* Without a spec, all this communication happens (because it has to) but it happens ad hoc
  * Programmers tend to give answers answers about how something works based on the implementation logic vs. how it "should" work
  * QA ends up testing the program against the program vs. against the design
  * Technical writers end up asking programmers how something works
  * All of these interruptions impact productivity
* Without a detailed spec is that it makes it impossible to make a schedule
* In many engineering organizations, every time there's a design debate nobody ever manages to make a decision (due to politics)
  * Engineers then tend to work on uncontroversial items
  * All the hard decisions are pushed to the end - these are the most likely projects to fail
* Example: everybody agrees that when a user forgets their password, you email a new password to them
  * This decision is not enough to implement this behavior as the implementer will need to know the text of the email
  * Marketer, PR person, etc is required to define the message
  * Good, complete, specs have these requirements
* In most organizations, the only "specs" that exist are one page text documents that the implementer wrote after implementing the logic and after explaining the feature to the three hundredth person

