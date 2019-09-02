# [How to handle forms with just React](https://medium.com/@everdimension/how-to-handle-forms-with-just-react-ac066c48bd4f)

* the `onSubmit` callback gets called when you submit the html `form` by clicking on the `button` or just by pressing `enter` while focused on one of the input fields
* adding the `name` attribute to inputs adds structure to form
* No `onClick` listener on the submit button
  * Not be able to respond to submit events triggered by the keyboard (pressing enter)
  * `onSubmit` callback handles both cases
* If server needs data in different format than input, handle it in the event handler (i.e. `handleSubmit`)
  * Build reusable set of `inputParsers` that parses date, or translates to uppercase, or to number
* `<form />` elements have an `elements` property (HTML collections object)
  * Provides access to all `input` nodes in form, by key where the key is the input `name` (`form.elements.birthdate` for `<input name='birthdate' />`)
  * Use `data-parse` data attribute on `input` - find parser that matches attribute value, and then use parser on value
  * Solution is not tied to any form-handling library
* Form validation
  * Add `noValidate` property to `form`
    * This prevents browser from interfering when an invalid form is submitted
  * Form is considered invalid when the `checkValidity` method returns `false`
    * Forms have their own `checkValidity` method
    * Inputs have their own `checkValidity` method
  * Can add `required` attribute (doesn't work in Safari or mobile Safari)
  * `pattern` attribute
    * Regex that's matched against input value
  * `type` attribute (like `email`)
* Displaying invalid inputs
  * `input:invalid { border-color: red }`
    * pseudo-selector is powerful, but dumb (no notion of "dirty" or previous attempts to submit)
  * Add `displayErrors` class to `form` after user has tried to submit and in CSS namespace namespace pseudo-selector with `displayErrors` class name
