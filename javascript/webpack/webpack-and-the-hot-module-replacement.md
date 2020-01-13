# [Webpack & The Hot Module Replacement](https://medium.com/@rajaraodv/webpack-hot-module-replacement-hmr-e756a726a07)

* When HMR is working, if change background of an `input` from `gray` to `green`, HMR will update the background without removing text inside `input` - in other words, the browser doesn't reload and the state of the DOM is kept as-is
* Webpack + HMR generates manifest and update files
  * `<hash>.hot-update.json` is the format for manifest files - these manifest files contain JSON that indicates what files have been updated
  * `<chunkNumber>.<hash>.hot-update.js` is the format for update files - these contain the JS to update / the DOM needs to apply
* `webpack-dev-server` communicates with client about updates via WebSocker
  * When WDS receives `Invalid` notifications that indicates that files have been updated
  * The update file contains the js that will be executed by the HMR runtime
    * The update file will call the `webpackHotUpdate` function
    * The update file will also contain the module id (like `88`) and the changes
    * HMR runtime will pass information to the correct loader (like the `style-loader` HMR runtime) that knows how to process change
