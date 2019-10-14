# [22 Performance Optimization Techniques for React](https://medium.com/better-programming/https-medium-com-mayank-gupta-6-88-21-performance-optimizations-techniques-for-react-d15fa52c2349)

* Use `React.memo` for component memoization
  * Can be used as a HOC
  * Second argument to `React.memo` is a custom comparator that determines whether or not to recalculate using the function specified in the first function

* Lazy load components using `lazy` and `Suspense`

```jsx
render() {
  if (foo) {
    ComponentToLazyLoad = lazy(() => import('./foo'));
  } else {
    ComponentToLazyLoad = lazy(() => import('./bar'));
  }

  return (
    <div>
      <Suspense fallback={<div>Loading</div>}
        <ComponentToLazyLoad />
      </Suspense>
    </div>
  );
}
```

* This will lazily load components when required and components are part of a separate chunk, loaded at runtime
* Login case
  * Component when user is logged in
  * Component when user is logged out
  * Instead of having both components in bundle file, delay loading component based on user's state

* Avoid Async Requests in `componentWillMount`
  * `componentWillMount` will be called right before render
  * Can be used to configure component, but this can also be done in constructor
  * Method cannot access DOM elements as component is still not mounted
  * Async requests should be in `componentDidMount`

* Adding arrow function to class definition means that each instance will have function
  * Since function is not on prototype property, function is not available in inheritance chain
  * Best way to use function binding in constructor

* Avoid using inline style attribute
  * Browser spends more time scripting and rendering

* Optimize conditional rendering
  * Diffing algorithm checks element at each position
  * So if ordering changes between conditions, React will unmount and remount identical components

* Enable gzip compression on web server
  * Will add `Content-Encoding` headers that will specify resource is encoded with gzip
  * Browser decompresses before rendering
  * While compression is not free, it's still generally recommended to use gzip compression

