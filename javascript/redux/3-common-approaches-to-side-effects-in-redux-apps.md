# [3 common approaches to side-effects in Redux apps](https://goshakkk.name/redux-side-effect-approaches/)

* Side-effect features 
  * A UI interaction may trigger a side-effect (i.e. clicking a button triggers an HTTP request)
  * When a process finishes, you may want to trigger a "SUCCESS" or "FAILURE" Redux action
  * Some side-effects may not dispatch anything - analytics based on Redux actions may not `dispatch` anything
* Three ways of handling side-effects
* Inside action creators
  * A "smart" action creator that `dispatch`es action and then does some HTTP request fetching (for example)
  * Pros
    * Simple
  * Cons
    * Action creator is no longer pure
    * Testing complexity
    * Interleaves action events with fetching events (or other types of logic)

```javascript
function fetchData() {
  return dispatch => {
    dispatch({ type: 'FETCHING_DATA' });
    fetch('/some_url')
      .then(data => dispatch({ data, type: 'FETCHED_DATA_SUCCEEDED' }))
      .catch(error => dispatch({ error, type: 'FETCHED_DATA_FAILED' }));
  };
}
```

* Listeners - code that waits for an action and performs side-effect when it sees the action
  * Pros
    * Can have several listeners for an action
    * Allows grouping similar side-effect logic in one place (avoid interleaving action creator logic with side-effect logic
  * Cons
    * Generally a new concept that has learning curve

```javascript
function fetchData() {
  return { type: 'FETCHING_DATA' };
}

// pseudo-code in some other part of app
when('FETCHING_DATA').do((dispatch) => {
    fetch('/some_url')
      .then(data => dispatch({ data, type: 'FETCHED_DATA_SUCCEEDED' }))
      .catch(error => dispatch({ error, type: 'FETCHED_DATA_FAILED' }));
});
```
* Specialized middleware - e.g. HTTP request middleware that looks for certain actions with certain types and payloads and executes HTTP requests
  * Pros
    * Action creators are still pure and simple
  * Cons
    * Requires special knowledge of how certain action creators are interpreted
    * Non-generalizable - usually only used for HTTP requests

```javascript
function fetchData() {
  return {
    type: ['FETCH_DATA', 'FETCHED_DATA_SUCCEEDED', 'FETCHED_DATA_FAILED'],
    url: '/some_url',
  };
}
```

* One benefit of listeners over smart action creators is decoupling
  * Imagine case where a save button is clicked
  * You want to process this event for analytics reasons - so you have a side-effect listening for this action
  * You also make an HTTP request that responds to this save event
  * Both of these side-effects are independent of each other
