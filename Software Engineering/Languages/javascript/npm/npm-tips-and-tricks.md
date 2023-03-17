# [NPM Tips And Tricks](https://blog.bitsrc.io/npm-tips-and-tricks-24c5e9defea6)

* Benefits of `npm ci`
  * Installs the exact version of the packge mentioned in `package-lock.json`
  * Removes existing `node_modules` and runs a fresh install
  * Won't write to `package.json` or lockfile
* Can install multiple packages with same prefix like `npm i eslint-{plugin-import,plugin-react,loader}`
* `npm run env` lists all environment variables present in package like `npm_package_license`, `npm_package_name`, etc
* Can run multiple scripts in parallel using `&` 
* Use `--` to pass arguments to an npm script
* `npm docs <package-name>` or `npm home <package-name>` will navigate to docs of specified npm package
  * `npm repo <package-name>` does something similar, except it goes to the GitHub repo page
* `npm bug <package-name>` will open issues page
* `npm dedupe` or `npm ddp` will remove duplicate packages and share common dependencies across multiple dependent packages
* `npm doctor` checks `node` and `npm` versions, validates the cache for any corrupt packages
* `npm outdated --long` checks for outdated packages
* `npm ls --depth-1` lists all packages but only at a depth level of `1`
