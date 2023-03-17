# [Using `nodemon`](https://alligator.io/workflow/nodemon/)

* https://github.com/remy/nodemon#nodemon
* Useful for `express` apps, will restart server
* Can specify a `nodemon.json` file
  * `watch` specifies which directories (and any subdirectories to watch)
  * `ignore` specifies directories (and any subdirectories to ignore)
  * `execMap` specifies any commands to run when files with a certain extension change

```json
{
  "watch": [
    "src"
  ],
  "ignore": [
    "build/**"
  ],
  "execMap": {
    "js": "npm run build && npm run start"
  }
}
```

* Can also specify `nodemon` configuration in `package.json` under `nodemonConfig` key
* `nodemon` will also look at `main` file defined in `package.json` and will use that as file to execute
  * `"main": "build/index.js"`
  * and then `dev` script is `"dev": "./node_modules/.bin/nodemon"`
