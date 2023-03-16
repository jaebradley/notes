# Syncing Redux Stores Across Browser Tabs

* Web application which runs in multiple Chrome tabs / windows
  * Tabs have `master` / `slave` relationship
    * `master` tab is allowed to trigger state changes whereas the `slave` tab can only receive changes and react to them
* Websocket server
  * `master` state changes lead to Redux actions
  * Redux middleware fires websocket to server
  * `master` Redux state changes
  * Each `slave` has a websocket listener
  * When it sees an action come in, it dispatches action to own Redux store
  * For bidirectional implementation, label source of action and then if tab receives own action, don't dispatch to store
* LocalStorage
  * All browser tabs / windows share the same `localStorage` object
  * LocalStorage middleware that sets an item in `localStorage` given relevant tab state change
  * LocalStorage listeners that check for `localStorage` changes and then fire those changes to store
