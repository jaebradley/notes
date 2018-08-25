# [Interaction](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch1.md#interaction)

* Often times you'll need the results of callback functions to interact in some deterministic way

```javascript
// Example of non-deterministic interaction
// No guarantee that blah and bluh will finish in any specific order

const responses = [];
function callback(response) {
  responses.push(response);
}

ajax('blah', callback);
ajax('bluh', callback);
```

```javascript
// Actually deterministic

const responses = [];
function callback(response) {
  if (response.url === 'blah') {
    responses[0] = response;
  }

  if (response.url === 'bluh') {
    responses[1] = response;
  }
}

ajax('blah', callback);
ajax('bluh', callback);
```
