# Implement Semantic Release In Your NPM Project

* Install `node 8` or above
  * [Why `node 8` or above](https://github.com/semantic-release/semantic-release#why-does-semantic-release-require-node-version--8)
* Install the [`semantic-release-cli`](https://github.com/semantic-release/cli) globally
* Run the `semantic-release-cli setup` command and follow the instructions to setup your GitHub, Travis (or other continuous integration service), etc.
* Check your `package.json`
  * Your `version` field should look something like `"version": "0.0.0-development"`
  * Your `scripts` field should contain `"semantic-release": "semantic-release pre && npm publish && semantic-release post"`
  * Make sure the `semantic-release`package is a `devDependency`
* Let's add a commit message formatter
  * Why? Because `semantic-release` uses the commit format to increment your package version. For example, here are [the default commit message formats that `semantic-release` ships with](https://github.com/semantic-release/semantic-release#default-commit-message-format)
  * By using [`commitizen`](https://github.com/commitizen/cz-cli) and [`commitlint`](https://github.com/marionebl/commitlint) we can create these formatted commit messages in a straight-forward manner using a command line tool as well as adding commit message linting as a git hook to prevent improperly formatted git commit messages from being committed.
    * After installing `commitizen` in your repository, make sure to install any commit message adapters you might like (like the conventional commit message adapter found at [`cz-conventional-changelog`](https://github.com/commitizen/cz-conventional-changelog)). [`commitizen` instructions for repository setup](https://github.com/commitizen/cz-cli#commitizen-for-project-maintainers).
      * If you do install a commit message adapter, make sure to add the following `config` field to your `package.json` file like so

      ```json
      "config": {
        "commitizen": {
          "path": "cz-conventional-changelog"
        }
      }
      ```

    * I would recommend using `commitlint` with a Git hook service like [`husky`](https://github.com/typicode/husky)
      * For example, you could implement `commitlint` to run on the `preparecommitmsg` `husky` hook (you can see [all `husky` hooks here](https://github.com/typicode/husky/blob/master/HOOKS.md)) by adding the following to the `scripts` field in your `package.json`: `"preparecommitmsg": "commitlint -e $GIT_PARAMS"`
      * Don't forget to add a `commitlint` config file in the base directory of your project (for example, `commitlint.config.js`) that defines the exact `commitlint` commit message formatting configuration you'll be using.
      * An example `commitlint.config.js` might look like

      ```javascript
      module.exports = {
        extends: ['@commitlint/config-angular'],
      };
      ```

      if you were to implement the Angular commit message convention
