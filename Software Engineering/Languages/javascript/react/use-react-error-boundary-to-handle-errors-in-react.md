# [Use react-error-boundary to handle errors in React](https://kentcdodds.com/blog/use-react-error-boundary-to-handle-errors-in-react)

* In order for a component to be an error boundary it must be a class component and must implement `getDerivedStateFromError` or `componentDidCatch`

```jsx
<ErrorBoundary
  FallbackComponent={ErrorFallback}
  onReset={() => {
    setUsername('');
    usernameRef.current.focus()
  }}
  resetKeys={[username]}
>
  <Bomb username={username}
</ErrorBoundary>
```

* Error boundaries do not catch errors for event handlers, asynchronous code like `setTimeout`, errors thrown in the error boundary itself (rather than children)
* Manage error state for async things (like API requests) using component state like

```jsx
function handleSubmit(event) {
  fetchGreeting(event.target.value).then(
    newGreeting => setState({ greeting: newGreeting, status: 'resolved' }),
    newError => setState({ error: newError, status: 'rejected' }),
}

return status === 'rejected' ? (
  // Render error state
) : status === 'resolved' ? )
  // Render success state
);
```

* Unfortunately, this means that need to maintain runtime errors and fetching / async errors
* `react-error-boundary` exposes a hook to incorporate async errors into the error state that it maintains

```jsx
const handleError = useErrorHandler();

function handleSubmit(event) {
 // stuff
 fetchGreeting(name).then(
   // on success
   error => handleError(error),
  );
}
```

* Can also propagate errors with the `useErrorHandler` hook like

```jsx
const { status, greeting, errors } = useGreeting(name);
useErrorHandler(error);
```

* This will call the nearest error boundary if `error` is truthy
