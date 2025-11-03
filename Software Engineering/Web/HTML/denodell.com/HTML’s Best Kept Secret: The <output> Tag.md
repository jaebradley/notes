# [HTML’s Best Kept Secret: The <output> Tag](https://denodell.com/blog/html-best-kept-secret-output-tag)
* `output` announces a value when it changes
  * It already has `aria-live="polite" aria-atomic="true"`
* This means that updates do not interrupt the user
  * The entire content is spoken rather than just the part that changed
* Like `label`, `output` has a `for` attribute
  * This `for` attribute takes a list of any `input` element `id`s that the result depends on
  * `<output for="a b"></output>`
* Doesn't require a form - can be used anywhere that dynamic text is updated based on the user's input
* `output` is tied to user inputs and actions not global notifications like toast messages
  * Global notifications are better handled with `role="status"` or `role="alert"` since they represent system feedback rather than calculated output
