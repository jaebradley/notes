# [What Is A Blob?](https://codeburst.io/javascript-interview-question-what-is-a-blob-f54482317e7f)

* Blob stands for `Binary Large Object`
* In JavaScript, blobs are used to represent files as immutable raw data
* Blobs have two properties, `size` and `type`
  * `new Blob([doc.output()], { type: 'application/pdf' })` - type needs to be the `MIME` type
* Blobs have a `text` method that returns a `Promise` that resolves the content of the blob as `text`
* When receiving blobs through `fetch` API, use the `response.blob` method `fetch('some url').then(response => response.blob())`