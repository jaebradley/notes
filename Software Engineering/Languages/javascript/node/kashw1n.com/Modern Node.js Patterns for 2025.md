# [Modern Node.js Patterns for 2025](https://kashw1n.com/blog/nodejs-2025/)

## Module System: ESM is the New Standard
* CommonJS required explicit exports and synchronous imports
  * `module.exports = { function1, function2, etc }`
  * Limitations were no static analysis, tree-shaking
* Built-in modules have a `node:` prefix
* Syntax is `export function abc`
* Top-level `await` is supported at the module level

```javascript
import { readFile } from 'node:fs/promises';

const config = JSON.parse(await readFile('config.json', 'utf8'));
const server = createServer(config);
console.log('App started with config:', config.appName);
```

## Native Fetch API
* No need for `axios` or `node-fetch`
* Also get behavior like timeout and cancellation support via `AbortSignal` and `AbortController`

```javascript
async function fetchData(url) {
  try {
    const response = await fetch(url, {
      signal: AbortSignal.timeout(5000) // Built-in timeout support
    });

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }

    return await response.json();
  } catch (error) {
    if (error.name === 'TimeoutError') {
      throw new Error('Request timed out');
    }
    throw error;
  }
}

// Cancel long-running operations cleanly
const controller = new AbortController();

// Set up automatic cancellation
setTimeout(() => controller.abort(), 10000);

try {
  const data = await fetch('https://slow-api.com/data', {
    signal: controller.signal
  });
  console.log('Data received:', data);
} catch (error) {
  if (error.name === 'AbortError') {
    console.log('Request was cancelled - this is expected behavior');
  } else {
    console.error('Unexpected error:', error);
  }
}
```

* Can use `AbortController` with file operations, database queries, any `async` operation that supports cancellation
