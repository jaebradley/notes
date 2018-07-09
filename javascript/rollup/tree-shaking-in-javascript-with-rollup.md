# [Tree-shaking In JavaScript With Rollup](https://justintimecoder.com/treeshaking-in-javascript-with-rollup/)

* Tree-shaking works by using ES6 modules
  * ES6 modules are static so imports and exports can be reliably tracked
* Classes, which are imported as one reference / symbol cannot be statically analyzed to remove members

```javascript
// library.js

import {
  a,
  b,
  c,
} from './helper'

export default function library() {
  // since function a isn't called by library
  // it won't get bundled
  b();
  c();
}
```
