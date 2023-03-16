# [Handling Fetch API Errors](https://www.tjvantoll.com/2015/09/13/fetch-and-errors/)

```javascript
fetch('http://httpstat.us/500')
  .then(function() {
    console.log('ok');
  }).catch(function() {
    console.log('error');
  });
// logs 'ok' and not 'error'
```

* `fetch` only rejects a promise when a network error is encountered - basically when the user is offline or some DNS lookup failure
* `fetch` has an `ok` property on it's `response` object to indicate if a response's status code was in the successful range

```javascript
fetch('http://httpstat.us/500')
  .then(function(response) {
    if (!response.ok) {
      throw new Error(response.statusText);
    }

    return response;
  }).then(function(response) {
    console.log(`Response succeeded:\n${response}`);
  }).catch(function(error) {
    console.error(error);
  });
```
