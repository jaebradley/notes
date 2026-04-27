# Chapter 2: First Class Functions

* When functions are called "first class" it means that we can treat functions like any other data type (like storing them in arrays, passing them around as function parameters, assigning them to variables)
* Common type of paradigm in JS is to have a callback function that is defined as an anonymous function like

```javascript
httpGet('some/url', json => renderPost(json))
```

* If this callback changed and now an `error` can get passed back, need to change every callback to take both `(json, err)`
* If the callback was a first class function, and defined explicitly beforehand, much less would have to change

```javascript
httpGet('/some/url', renderThing)
```

* These two functions do exactly the same thing, but one feels more general and reusable

```javascript
// specific to current blog
const validArticles = articles => articles.filter(article => article != null && article !== undefined);

// more relevant for future projects / reusable
const compact = xs => xs.filter(x => x !== null && x !== undefined);
```

* By using specific naming, it's tied ourselves to specific data