# [Async React using React Router & Suspense](https://itnext.io/async-react-using-react-router-suspense-a86ade1176dc)

* Traditional way of loading component synchronously is to import component, and then render it within the `App` component
  * Side-effect is that it might balloon bundle size if too many imported modules wouldn't normally be displayed
* To grab component after App component has loaded use `lazy` and `Suspense` like

```jsx
import React, { lazy, Suspense } from 'react';

const SomeComponent = lazy(() => import('./SomeComponent'))

const App = () => (
  <Suspense fallback={SomeFallbackComponent}
    <SomeComponent />
  </Suspense>
);
```

* While `Suspense` component loads lazy-loaded `SomeComponent`, it will display the fallback component
* Webpack automatically code-splits bundle when using lazy

## Async React Router

* Ideally you'd only want to load the components you need for a given route when _at_ that specific route

```jsx
const FirstComponent = lazy(() => import("./FirstComponent"))
const SecondComponent = lazy(() => import("./SecondComponent"))
const ThirdComponent = lazy(() => import("./ThirdComponent"))

<Suspense fallback={<SomeFallbackComponent />}>
  <Switch>
    <Route path="/1">
      <FirstComponent />
    </Route>
    <Route path="/2">
      <SecondComponent />
    </Route>
    <Route path="/3">
      <ThirdComponent />
    </Route>
  </Switch>
</Suspense>
```

* Writing library to do dynamic `import`s is tough because `import` checks where file is loaded from where `import` function is called
  * Library would only work for files relative to library in `node_modules`

```jsx
const lazyImport = (filename) => lazy(() => import(`${filename}`));
```

* Webpack requires static compilation so strange template-string code is necessary
