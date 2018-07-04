# [The Saga Continues: Magic In React](https://medium.com/@js_tut/the-saga-continues-magic-in-react-44da8d134285)

* Sagas use `yield` and generator functions to dispatch events asynchronously
* `yield call` executes API call
* `yield put` takes the fetched data and dispatches a Redux action to be used by a reducer
* `yield fork` creates attached forks, which are tasks that execute in the background
  * My understanding after reading the article is that in the main sagas file, i.e. `sagas/index.js`, `yield fork` is used to, essentially, create all the sagas the application is going to use
  * `fork` is combined with `takeLatest` which basically spawns a saga on each dispatched action - it also cancels any previous saga tasks if they're running

```javascript
export function *sagas() {
  yield [
    fork(takeLatest, 'sagaName', firstSaga),
    ...
  ]
}
```

* The way I interpret the saga workflow
  * Saga events / actions are dispatched from a React component
  * The matching saga is identified in `sagas/index.js`
  * This saga is executed, and if necessary, it updates some state via a reducer
  * This state update triggers `props` to get updated via `mapStateToProps`
* Saga events + Reducer events follow a post/pre-fixed pattern like `UPDATE_USER_FETCH` (saga event) and `UPDATE_USER_PUT` (reducer event)
