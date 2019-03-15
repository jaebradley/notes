# [Five Tips For Working With Redux In Large Applications])https://techblog.appnexus.com/five-tips-for-working-with-redux-in-large-applications-89452af4fdcb

* Don't store data as plain arrays - use a `Map`
  * Easy to find single user
* Scaling reducer files vertically (adding more lines of code) is less maintainable than scaling reducers horizontally
  * Breaking reducers into their own files makes it easier to reuse those reducers
* Separate canonical data (i.e. data returned from the server) from edit data (`isEditing` application state)
  * Don't have to deal with merging updated canonical state back into store
  * User edits name, then cancels their edit - if server data and edit data are stored in same property, then rolling back to previous state is difficult
* Interesting way of generalizing that state is set across different parts of application
  * Reduce overhead / boilerplate - look for common state patterns that are colocated in same file / part of store

```javascript
const initialLoadingState = {
  usersLoading: false,
  domainsLoading: false,
  subDomainsLoading: false,
  settingsLoading: false,
};

// Reducer
const loadingReducer = (state = initialLoadingState, action) => {
  const { type, payload } = action;
  if (type === SET_LOADING) {
    return Object.assign({}, state, {
      // sets the loading boolean at this scope
      [`${payload.scope}Loading`]: payload.loading,
    });
  } else {
    return state;
  }
}

// Action Creator

const setLoading = (scope, loading) => {
  return {
    type: SET_LOADING,
    payload: {
      scope,
      loading,
    },
  };
}
// example dispatch call
store.dispatch(setLoading('users', true));
```
