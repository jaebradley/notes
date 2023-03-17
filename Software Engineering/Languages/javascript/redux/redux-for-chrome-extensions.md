# [Redux For Chrome Extensions](https://robots.thoughtbot.com/redux-for-chrome-extensions)

* Chrome extension is composed of a manifest file, at least one HTML file, and can include JS / CSS files
  * Extensions can also add popups
    * Popups are not long-lived as they are disposed by the browser after they are closed
* JavaScript running on a popup page cannot communicate directly with the background script since they run in different contexts
  * Need to communicate through message passing (`react-chrome-redux` wraps chrome message passing around Redux mechanics)
* Note that in the webpack config, there are two entry points - one for the background page and one for the popup page

```javascript
module.exports = {
  entry: {
      background: [
        'babel-polyfill',
        `${PAGES_PATH}/background`,
      ],
      popup: [
        'babel-polyfill',
        `${PAGES_PATH}/popup`,
      ]
    },
}
```

* `react-chrome-redux` maps actions from the popup page (to the background page) to some function to execute
* Remember that while background page persists, popup page is instantiated every time it is interacted with

```javascript
// Example basic setup for Popup page
import React from 'react'
import ReactDOM from 'react-dom'
import {Provider} from 'react-redux'
import {Store} from 'react-chrome-redux'

import App from './app'

const store = new Store({
  portName: 'BOOKMARKSAVER',
})

store.ready().then(() => {
  const mountNode = document.createElement('div')
  document.body.appendChild(mountNode)

  ReactDOM.render(
    <Provider store={store}>
      <App />
    </Provider>,
    mountNode
  )
})
```
