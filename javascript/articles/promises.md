# [Promises](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-promise-27fc71e77261)

## What is a `Promise`?

* A `Promise` is an object which can be returned synchronously from an asynchronous function
* It is either

  1. __Fulfilled__ - the `resolve` method was called
  2. __Rejected__ - the `reject` method was called
  3. __Pending__ - is not fulfilled or rejected

* A `Promise` is __settled__ if it is not pending (it's either fulfilled or rejected)
* Once a `Promise` is settled it is essential immutable - calling `resolve` or `reject` has no effect
  * It must have a value (which could be `undefined`) which must not change
* The `reject` method of a `Promise` should always throw an `Error`

## What is the `then` method?

* Every `Promise` must be thenable
* The `then` method returns ` Promise`
* The `then` method takes two optional parameters an `onFulfilled` function and an `onRejected` function
* The `onFulfilled` function will be called after the `Promise` is fulfilled with the `Promise`'s value as the first argument
* __Always call the `catch` method__
  * If the `catch` method is not called, any error that occurs when the `onFulfilled` method is called gets swallowed


## Examples

### Making an HTTP request

```javascript
// This is the library we use to make HTTP requests
// https://github.com/sindresorhus/got
const got = require('got');

// https://jsonplaceholder.typicode.com/ has a "fake" JSON API
got('https://jsonplaceholder.typicode.com/posts/1')
  .then((response) => {
    // Once I get my response, I want to parse the JSON body
    // Note how I don't specify an `onRejected` method - I am not planning on
    // throwing a custom error in the case that the something goes wrong with the request
    const parsedBody = JSON.parse(response.body);
    console.log(`Parsed Body: ${JSON.stringify(parsedBody, null, 2)}`);
    // Since every then method returns a Promise, the returned Promise will be the title field, but upper cased
    return parsedBody.title.toUpperCase();
  }).then((upperCaseTitle) => {
    console.log(`UPPERCASE TITLE: ${upperCaseTitle}`);
    // Returns a Promise with lower cased title field
    return upperCaseTitle.toLowerCase();
  }).then((lowerCaseTitle) => {
    console.log(`lowercase title: ${lowerCaseTitle}`);
    // This also returns a Promise, but with a value of undefined
  }).catch((error) => {
    console.error(error);
  });
```

### Errors when making HTTP request

```javascript
const got = require('got');

got('https://jsonplaceholder.typicode.com/posts/1')
  .then((response) => {
    const parsedBody = JSON.parse(response.body);
    console.log(`Parsed Body: ${JSON.stringify(parsedBody, null, 2)}`);
    console.log('right before error');
    throw new Error('foobarbaz');
    console.log('right after error');
    // Should not get here...
    return parsedBody.title.toUpperCase();
  }).then((upperCaseTitle) => {
    // ...or here...
    console.log(`UPPERCASE TITLE: ${upperCaseTitle}`);
    return upperCaseTitle.toLowerCase();
  }).catch((error) => {
    // ...but should get here!
    console.error(error);
  });

// Expected console statements
// Parsed Body: ...
// right before error
// Error: foobarbaz
```
