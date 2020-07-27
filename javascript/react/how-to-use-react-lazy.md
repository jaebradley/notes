# [How To Use React.lazy](https://medium.com/@MCapoz/how-to-use-react-lazy-6ff434aeed51)

* `React.lazy` allows the blocking of UI from rendering until some condition is met (i.e. don't show some UI until response from the server is received)
  * Before `React.lazy`, all files / components would need to be loaded, even if they were not initially going to be visible
* `React.lazy` allows dynamic importing of components so that they are only loaded when they are necessary
* Import `Suspense` and `lazy` from `React`
* Defer loading by doing something like `const DeferredComponent = lazy(() => import("./SomeComponent"))`
* Wrap the component in a `Suspense` with a fallback
  * The `Suspense` component waits for the dynamically loaded code to finish loading while showing the loading state / fallback in the time it takes to complete loading

```jsx
<Suspense fallback={<div>I am fallback</div>}>
  <DeferredComponent />
</Suspense>
```