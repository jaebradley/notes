# [A Simple React Router v4 Tutorial](https://blog.pshrmn.com/simple-react-router-v4-tutorial/)

* `BrowserRouter` should be used when server will be handle dynamic requests
  * Generally preferable to use `BrowserRouter`
* `HashRouter` should be used for static websites (where server will only respond to requests for files that it knows about)
* Each router creates a history object, which keeps track of current location and will re-render website when it changes
  * React Router components depend on having the history object available through React context, so they must be rendered as descendants of router component
  * In other words, a React Router component that does not have a router as one of its ancestors will fail to work
* Router components only expect to receive a single child element
  * Useful to create an `App` component that renders rest of application that is child of Router
  * Also useful to separate `App` component if ever wanted to switch to server-side rendering because can re-use `App` when server-side rendering while switching router to use `MemoryRouter`

## Route Component

* Anywhere that conditional rendering logic exists that depends on location pathname, use `Route` component
* Expects a `path` prop that describes pathname that route matches
  * Whenever current location pathname matches, Route will render any associated content
  * Only cares about the pathname and _not_ query parameters
* `Switch` component groups `Route`s
  * `Switch` component will iterate over child elements and render first one that matches the current pathname
* Three ways to render content
  * Defining a React component via the `component` prop
  * A `render` prop that takes a function which returns a React element - can pass extra information to this function from "outer" context
  * `children` prop which is also a function that returns a React element
    * However, this function will always render, regardless if route matches
  * Elements rendered by `Route` are passed the `match` object, the current `location` object and the `history` object as props
    * The `match` object will contain any dynamic path parameters like `/roster/:number`
* Nested routes should be represented by sub-components i.e. components that return a `Switch` component with its own `Route` groupings
