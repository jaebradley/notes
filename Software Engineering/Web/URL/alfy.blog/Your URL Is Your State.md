# [Your URL Is Your State](https://alfy.blog/2025/10/31/your-url-is-your-state.html)
* Bracket notation, represents arrays in query parameters
  * Originated from early web frameworks like PHP where appending `[]` to a parameter name signals that multiple values should be grouped together
  * `?ids[0]=a&ids[1]=b`
  * Many modern web frameworks and parsers recognize this pattern, but it is not officially standardized in the URL specification
* Poor candidates for URL state
  * Temporary UI states, like an open modal or an expanded dropdown
  * High frequency transient states like mouse position or scroll position
* Use the `popstate` event listener to handle back/forward buttons when rendering content based on query parameters
* Use `window.history.pushState`  to update the URL without reloading the page when updating query parameters
* Don't pollute URLs with default values
* Debounce URL changes, like search as you type
  * Can also decide to use `replaceState` instead of `pushState` to avoid polluting the window history
  * `pushState` makes the most sense for distinct navigation actions like changing filters, paginating, or navigating to a new view
* Readable URLs explain themselves - a human can read it and understand what they're looking at
* The order of query parameters can also provide analytics
* For example, `category=foo&price=bar`  might suggest that the user first added the category filter and then added the price filter
