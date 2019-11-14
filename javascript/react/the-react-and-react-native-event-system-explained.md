# [The React and React Native Event System Explained: A Harmonious Coexistence](https://levelup.gitconnected.com/how-exactly-does-react-handles-events-71e8b5e359f2)

* React listens to every event at the `document` level
  * By the time any `React`-related code has executed, events have already gone through DOM event handling
* React normalizes browser-specific implementation details for events
* `EventPluginHub`
  * Provides common interface for plugins to be injected
  * Executes injected plugins for every new event, collecting generated `SyntheticEvents`, and then dispatching these `SyntheticEvents`
    * Each plugin's `SyntheticEvent`s and dispatch functions are stored in a queue
* Event Plugins
  * Ingest native DOM events
  * Translate events into `SyntheticEvent`s
  * Collect all functions to dispatch (i.e. event handlers)
  * Return `SyntheticEvent`s to `EventPluginHub`
  * Plugins don't dispatch events
* React collects dispatches by traversing component tree to identify capture event handlers and bubbling event handlers
* `stopPropagation` only takes effect at dispatch time, stopping subsequent functions for a given `SyntheticEvent`
* `SyntheticEvent`s are "scoped" to the plugin that created it
  * Only native event information is passed from plugin to plugin so plugins cannot modify `SyntheticEvent`s
* Scenario where two plugins share event type but will see differing behavior for `stopPropagation`
  * Plugins `A` and `B`
  * `onEventA` and `onEventB` for bubbling phase
  * `onEventACapture` and `onEventBCapture` for capturing phase
  * `click` triggers both and plugins are ordered `[A, B]`
  * If there was an `onEventACapture` `stopPropagation` below an `onEventA` and an `onEventB` the `onEventB` event handler would still be invoked
  * This is because `B` is a different plugin and has different `SyntheticEvent`s
* `SyntheticEvent`s are pooled which means that the object will be reused (i.e. it's properties will be nullified) after the event callback is invoked
  * Thus, cannot access event in an asynchronous way

