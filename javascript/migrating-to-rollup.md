Recently I migrated [the `GitHubLanguagesClient`](https://github.com/jaebradley/github-languages-client) to use [`rollup.js`](https://rollupjs.org/guide/en) ([`github-langauges-client#18`](https://github.com/jaebradley/github-languages-client/pull/18))

A few things I learned:

* I split out my `babel` configuration for the `test` and `production` environments

```json
{
  "env": {
    "test": {
      "presets": [
        [
          "@babel/preset-env",
          { "modules": "umd" }
        ]
      ]
    },
    "production": {
      "presets": [
        [
          "@babel/preset-env",
          { "modules": false }
        ]
      ]
    }
  },
  "ignore": [
    "node_modules/**"
  ]
}
```

* This is because `jest` fails when `"modules"` is set to `false` but `rollup.js` requires that `"modules"` be set to `false`
* The client uses an underlying `JSON` file - in order to convert this file to an `ES6` module, I used [`rollup-plugin-json`](https://github.com/rollup/rollup-plugin-json)
