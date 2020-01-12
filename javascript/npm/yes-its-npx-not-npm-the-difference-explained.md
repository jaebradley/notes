# [Yes, it’s npx, not npm — the difference explained](https://medium.com/javascript-in-plain-english/yes-its-npx-not-npm-the-difference-explained-58cbb202ec33)

* `npm` is a package manager for node whose goal is automated dependency and package management
* `npx` is a tool for _executing Node packages_
  * It comes bundled with `npm` starting from `npm@5.2`
* `npx` will check if package in project and will execute it if it exists
  * If package does not exist, it will install latest version and execute it
    * Can prevent automatic installation by using the `no-install` flag (there are other flags / configurations
* Instead of doing something like `./node_modules/bin/my-package` can just do `npx my-package`
  * This applies to `scripts` definitions in `package.json` file(s) which often times have these `./node_modules/bin/some-package` references
