# [Understanding `redux-saga` from action creators to sagas](https://blog.logrocket.com/understanding-redux-saga-from-action-creators-to-sagas-2587298b5e71)

* Sagas are a design pattern that comes from the distributed transactions world,
  * Saga manages processes that need to be executed transactionally
  * Keeps the state of the execution and compensates for failed processes

* ES6 generators
  * Functions that can be paused and resumed (instead of executing all statements in one pass)
  * When a generator function is invoked it returns an iterator object
    * By calling the `next` method on this iterator, the generator will execute until the next `yield` statement and pause

```javascript
function* myGenerator() {
  const first = yield 'first yield value';
  const second = yield 'second yield value';
  return 'third returned value';
};

const iterator = myGenerator();
console.log(iterator.next()); // { value: 'first yield value', done: false }
console.log(iterator.next()); // { value: second yield value', done: false }
console.log(iterator.next()); // { value: third returned value', done: false }
console.log(iterator.next()); // { value: undefined, done: true }
```

* Different types of sagas
  * Watcher sagas that, well, *watch* for dispatched actions
  * Worker sagas that, well, do some work after a watcher saga triggers them
    * In the example below, `takeEvery` will execute `fetchDogAsync` every time it sees the `FETCHED_DOG` action
    * If it sees multiple `FETCHED_DOG` actions it will start multiple instances of the worker saga

```javascript
// watcher saga that triggers operations
function* watchRequestDog() {
  yield takeEvery('FETCHED_DOG', fetchDogAsync);
};

function* fetchDogAsync() {
  // performs some task
}
```

## Implementing `fetchDogAsync` in `redux-saga`

* The `fetchDogAsync` might be implemented like

```javascript
function* fetchDogAsync() {
  try {
    yield dispatch(requestDog());
    const data = yield fetch(...);
    yield dispatch(requestDogSuccess(data));
  } catch (error) {
    yield dispatch(requestDogError());
  }
};
```

* But `redux-saga` yields an object that declares intent o preform an operation, like fetching data, rather than the result of fetching that data.

```javascript
function* fetchDogAsync() {
  try {
    yield puts(requestDog());
    const data = yield call(() => fetch(...));
    yield put(requestDogSuccess(data));
  } catch (error) {
    yield put(requestDogError());
  }
}
```

* `call` will return a plain old object that describes the operation that `redux-saga` will invoke and return the result of to the generator
* `put` returns an object with instructions for the middleware to dispatch the action
* These returned objects are called *Effects* - they look like

```javascript
{
  CALL: {
    fn: () => { /* do something here */ },
    args: [],
  },
}
```

* Easier to test `Effects` (i.e. a function that returns an object) than a method that makes an asynchronous call
