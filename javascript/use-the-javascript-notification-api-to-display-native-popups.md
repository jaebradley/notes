# [Use the JavaScript Notification API to Display Native Popups](https://levelup.gitconnected.com/use-the-javascript-notification-api-to-display-native-popups-43f6227b9980)

* Create a new notification using the `Notification` constructor
  * First argument is the title, the second is object that can take a variety of optional properties
* Request permission with `Notification.requestPermission` static method - returns `Promise` which resolves when permission is allowed / denied
  * If permission is denied, then `Notification` constructor can not be called successfully
* Notifications can be called by calling the `close` method on a `Notification` object
* `Notification` objects have their own event handlers for the `onclick`, `onclose`, `onerror`, and `onshow` events
  * Default behavior for `onclick` is to move focus to the viewport of the notification's browsing context (can call `preventDefault` to avoid this behavior)
  * The `show` event is fired when the notification is displayed