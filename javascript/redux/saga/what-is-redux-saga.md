# [What is `redux-saga`?](https://engineering.universe.com/what-is-redux-saga-c1252fc2f4d1)

* > Contrary to redux thunk, you don't end up in callback hell, you can test your asynchronous flows easily and your actions stay pure

## `redux-thunk` API request that fetches data on button click

```javascript
import {
  API_BUTTON_CLICK,
  API_BUTTON_CLICK_SUCCESS,
  API_BUTTON_CLICK_ERROR,
} from './actions/consts';
import { getDataFromAPI } from './api';

const getDataStarted = () => ({ type: API_BUTTON_CLICK });
const getDataSuccess = data => ({ type: API_BUTTON_CLICK_SUCCESS, payload: data })
const getDataError = message => ({ type: API_BUTTON_CLICK_ERROR. payload: message });

const getDataFromAPI = () => {
  return dispatch => {
    dispatch(getDataStarted());

    getDataFromAPI()
      .then(data => {
        dispatch(getUserSuccess(data));
      }).fail(err => {
        dispatch(getDataError(err.message));
      })
  };
};
```

* Fire action letting store know that we're about to make an API call
* Make API request, returning a `Promise`
* Fire success action, along with API response or error action with error response if error occurs

## Same thing but using `redux-saga`

```javascript
import { call, put, takeEvery } from 'redux-saga/effects';

import {
  API_BUTTON_CLICK,
  API_BUTTON_CLICK_SUCCESS,
  API_BUTTON_CLICK_ERROR,
} from './actions/consts';
import { getDataFromAPI } from './api';

export function* apiSideEffect(action) {
  try {
    const data = yield call(getDataFromAPI);
    yield put({ type: API_BUTTON_CLICK_SUCCESS, payload: data });
  } catch (e) {
    yield put({ type: API_BUTTON_CLICK_ERROR, payload: e.message });
  }
}

// the 'watcher' - on every 'API_BUTTON_CLICK' action, run our side effect
export function* apiSaga() {
  yield takeEvery(API_BUTTON_CLICK, apiSideEffect);
}
```

* call `put` instead of `dispatch` (essentially equivalent)
* "watcher" function that listens to `API_BUTTON_CLICK` action - function is assuming that action of this type will be fired by button's `onClick` handler
* "call" effect gets data from any async function (promise, different saga, etc.)
* A little easier to read `saga` example - no longer have to deal with promise chain
* saga side effect function is now a pure function since `call(getDataFromAPI)` doesn't actually request API request but returns object that looks like `{ type: 'CALL', func, args }`
* Actual fetch execution is taken care of by saga middleware, and will return value back to generator or throw
* Makes testing a lot easier, especially compared to `thunk` where we'd have to mock `getDataFromAPI`

## tests for `redux-saga` example

```javascript
import { call, put } from 'redux-saga/effects';

import { API_BUTTON_CLICK_SUCCESS, } from './actions/consts';
import { getDataFromAPI } from './api';

it('apiSideEffect - fetches data from API and dispatches a success action', () => {
  const generator = apiSideEffect();

  expect(generator.next().value)
    .toEqual(call(getDataFromAPI));

  expect(generator.next().value)
    .toEqual(put({ type: API_BUTTON_CLICK_SUCCESS }));

  expect(generator.next())
    .toEqual({ done: true, value: undefined });
});
```
