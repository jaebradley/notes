# [When TDD doesn't work](https://blog.cleancoder.com/uncle-bob/2014/04/30/When-tdd-does-not-work.html)

* TDD is like double-entry bookkeeping - state logic in test and in production code
  * Running tests is similar to creation of a balance sheet, if the balance of assets and liabilities aren't zero, there is a mistake somewhere

## Bell Example

* Writing tests for software that rings bell
* Connect a microphone, and write some software to detect bell
* Manual testing is "testing" the software by waiting for the bell to ring
* Unit tests that mock out bell driver, and unit tests that confirm that software sends appropriate signals to the driver at the appropriate times
  * These unit tests "prove" that the software _should_ ring the bell
* To test whether the "right stuff" is drawn on a screen, have to set up a camera and write code that can interpret what the camera sees, or look at the screen while running tests (manual)
  * Screen example is analogous to the "bell" example

## Boundaries

* The boundaries of a system, the I/O devices, that require manual testing
* Once software controls something that physically interacts with the world, automated tests become so impractical that manual tests are the best option
* Writing tests for the layer _before_ the physical world are a waste of time
  * Writing tests that ensure the CSS for a page is correct is almost certainly a waste of time due to the fact that in order to write the test, you have to know the contents of the CSS
  * If you want to test that the width for a certain element is `5px` then `5px` must appear in both the CSS and the test
  * Additionally, almost impossible to write the CSS test first - usually what happens is some initial CSS is written, fiddled with, until it looks right (eyes + mind are the actual test)
* Near the physical boundary of a system is a layer that requires fiddling and is pointless to write tests first

## TDD is not appropriate for test code

* Don't write tests for setting up test doubles, fixtures, etc
* Unit tests and production code are the tests for this "support code"

## Strip out logic from these layers that don't support TDD

* Remove from these layers, exporting the logic to modules that _are_ testable
* Keep these layers "humble" - don't put any unnecessary logic in these layers
  * Separating code that must be fiddled with from code that can be tested
  * These two domains will change for very different reasons, and at different rates, so it is wise to separate them

