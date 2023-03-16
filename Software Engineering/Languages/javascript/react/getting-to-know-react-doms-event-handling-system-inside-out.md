# [Getting to know React DOM's event handling system inside out](https://medium.com/the-guild/getting-to-know-react-doms-event-handling-system-inside-out-378c44d2a5d0)

* React uses a single event handler to handle each event type
  * There might be many components with an `onClick` handler but there's only one `click` event listener registered on the DOM
* React normalizes events into "synthetic events" so that regardless of browser, events have a consistent API
* React dispatches events to their handlers using the `EventPluginHub` which asks all plugins if they need to handle an event
  * `ChangeEventPlugin` handles `onChange` events, for example
* The `EventPluginHub` also notes what listeners and components care about that event, and then dispatches events, invoking the defined event handlers
  
