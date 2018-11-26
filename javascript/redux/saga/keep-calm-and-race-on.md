# [Keep Calm and Race On](https://levelup.gitconnected.com/keep-calm-and-race-on-redux-saga-case-study-2f5b0a6630ae)

* Polled requests to server - like after a file upload
* Polling logic looks like

```javascript
const jobSucceeded = false;

while (!jobSucceeded) {
  yield put('SOME_START_POLLING_REQUEST_ACTION');
  const pollingAction = yield take('SOME_POLLED_RESPONSE_ACTION');
  if (pollingAction.successful) {
    jobSucceeded = true;
    // Can put some action here and exit function
  } else if (pollingAction.errored) {
    jobSucceeded = true;
    // put some other action here and exit function
  }
  // Delay next poll by 1 second
  yield call(delay, 1000);
}
```

* `take` blocks execution of saga until specified action is dispatched
* Now we want to limit polling time to 1 minute and allow user to cancel file
* To implement these features use `race` effect
  * There's a "race" between the user cancelling the request, the 1 minute time limit expiring, the request erroring, and finally, the request succeeding
  * Cancelling the request by clicking a button can dispatch an action that the `race` can listen for
  * The 1 minute time limit could simply be a `delay(60000)`
  * If the request errors we can dispatch an action that the `race` can listen for
  * If the request succeeds we can dispatch an action that the `race` can also listen for

```javascript
const {
  response,
  failed,
  timeout,
} = yield race({
  response: call(makeSomePollingRequest),
  cancel: call('SOME_CANCEL_POLLING_ACTION'),
  failed: take('SOME_POLLING_FAILED_ACTION'),
  timeout: call(delay, 60000),
});

if (failed) {
  // Handle failure in some way
}
```
