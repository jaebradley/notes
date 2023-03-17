# [Common Mistakes With React Testing Library](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library)

* Don't use `wrapper` as the name of the return value from `render`
  * Artifact from `enzyme` - destructure what you need from `render` or call it `view`
* `cleanup` happens automatically
* Use `screen` as the `render` call destructure values no longer need to be kept up to date
* Use `@testing-library/jest-dom` as it has assertions like `toBeDisabled` that eliminate the need for things like `expect(button.disabled).toBe(true)`
* `render` and `fireEvent` are already wrapped in `act` so not helpful
* Querying by a query selector makes the test harder to read / break more frequently
* Query by text rather than test ids 
  * Ensures that translations are getting applied correctly
* Querying elements by their accessible name - this is what screen readers will read for the element even if the text content is split up by multiple elements (`<span>Hello</span><span>World</span>`)
* Use `@testing-library/user-event` and use `type` method which will trigger `keyDown`, `keyPress`, and `keyUp` events (closer to user's actual intentions)
* The _only_ reason to use the `query*` variant of the queries is to validate that an element is _not_ found
* Use `find*` to query for something that may not be available right away
* Wait for a specific assertion inside `waitFor`
