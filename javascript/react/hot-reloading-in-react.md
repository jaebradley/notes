# [Hot Reloading in React](https://medium.com/@dan_abramov/hot-reloading-in-react-1140438583bf)

* React hot loader - first tool to edit React component files without losing state or unmounting components

## Webpack HMR

* Hot module reloader is basically saying when a new version of some module I import is available, run a callback in my app so I can do something with it
* HMR is fancy way of polling dev server, injecting new `script` tags with updated modules and running callback in existing code
  * `module.hot.accept('./ThingWithNewVersion', () => doSomeCallbackWork())`
* If a module does not specify how to handle updates to itself, modules that imported it get included in hot update bundle
* Edit `Button.js` component imported by `UserProfile` and `NavBar`
  * Both are imported by `App`
  * If `index`.js` imports `App` and it has an `module.hot.accept('./App', someCallback)`, webpack will generate update bundle with all files and run callback

```javascript
// Whenever a new version of App.js is available
module.hot.accept('./App', function () {
  // Require the new version and render it instead
  var NextApp = require('./App')
  ReactDOM.render(<NextApp />, rootEl)
})
```

* Because `NextApp !== App` to React, it seems like a completely different component is being rendered and React unmounts previous one
  * Unmounting will destroy DOM and local state of components
* Two ways to preserve DOM and local state
  * Detach React instances from DOM nodes and state, create new instances of new component types, attach those new instances to existing DOM and state
  * Create "proxies" for every React type that wrap existing instance and when new updated instances are generated, point the proxy to use that instance as it's internal reference

### Detaching React Instances

* More realistic if React state subscriptions were independent of lifecycle hooks or if React did not rely on classes and instances as much
* Subtle problems like how to deal with subscriptions in `componentDidMount 
* Even if there was a way of replacing old instances with new instances without destroying DOM or state, old instances would still keep existing subscriptions and new instances would not be subscribed at all
* To subscribe new instances, need to fire lifecycle hooks on them during replacement
* However, `componentDidMount` would run twice on same DOM nodes, which could break assumptions that React components tend to make

### Proxying Component Classes

* Every React component class gets wrapped in a proxy
* Proxy is just a class that acts like component class
* Proxy has hooks to inject a new implementation of the class
* Existing instances start to act like new version of the class
* React local state and DOM are preserved
* Wraps every React component in `module.exports` into a proxy and exports the proxied component instead
  * When `App` renders `NavBar` it really renders `NavBarProxy`
  * Calls `NavBar#render` from `NavBarProxy#render`
  * Changes internal reference whenever `NavBar.js` module is updated
* Proxies are stored globally using an ID from filename and component `displayName`

### Problems with Proxying

* Supporting HOCs
  * RHL didn't see wrapped components in `module.exports` and didn't create proxies for them
  * So inner components would have their DOM and local state destroyed on changes
* Supporting local components that aren't exported
  * These too would not be wrapped in proxies and would cause subtrees to lose DOM and state

## Integrating with Babel

* Some kind ove static analysis is needed to locate and wrap React components, even if they aren't exported directly
* Also needed to implement error handling
* Wrap component in some type of proxy and wrap component's `render` in a `try/catch` i.e. a function that takes a component class and does something with it
* Create Babel plugin that identifies React components and wraps them in arbitrary customizable transforms

## Difficulties wrapping components statically

* Any function that returns a valid `ReactElement` is potentially a component
* Hard to avoid false positives even when applying heuristics like "uses JSX"
* Hard to wrap things statically
  * Many ways for functions and classes to be exported in JS (default / named exports, function declarations, arrow functions, class declarations, etc.)

## Missing Piece: Proxy at Call Site

* Finding and wrapping React components in source control is hard to do and potentially destructive and can break code
* Tagging components is relatively safe
* Babel plugin that adds a `register(uuid, Component)` at the end of very module for anything that looks like a component

```jsx
class Counter extends React.Component {
// Component definition
}
const defaultExport = someHOC(Counter)
export default = defaultExport;

// Generated transpiled code has register calls
register('Counter.jsx#Counter', Counter);
register('Counter.js#exports#default', defaultExport);
```

* `register` will basically check whether value is at least function, and if so, create a React Proxy for it
* Won't replace class / function
* Will wait until `React.createElement` is called
  * This is proof that it really is a React component
  * Update `React.createElement` to check if element has been proxied via uuid and if it has been, use the type / component that has been proxied
  * If no proxy exists, use type / component that is passed as argument
* Benefits / solving previous problems
  * No false positives for components as wrapping occurs only when component is "used"
  * Solves DOM reset / state reset problems with functional components
