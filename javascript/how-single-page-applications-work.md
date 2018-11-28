# [How Single Page Applications Work](https://medium.com/@pshrmn/demystifying-single-page-applications-3068d0555d46)

* A Single Page Application is a website where navigation does not trigger requests to the server to fetch new HTML
* Location parts
  * `https://www.example.com/some?key=value#foobar`
  * `https` is the **protocol**
  * `www.example.com` is the **hostname**
  * `/some` is the **path**
  * `?key=value` is the **search**
  * `#foobar` is the **hash**

## How Browser Navigation Normally Works

* Each browser tab has it's own array of session entries in the session history
* Each entry is made up of a reference to the `Document` object for that page and the location of the page
* When a link is clicked, a new entry is created
* When the back button is clicked, the previous entry is rendered using it's `Document` object and location
  * This `Document` object is created by the browser after processing the response from the server
* For the current entry, if a link is clicked that goes to a location that does not match the next entry, all those entries are removed

## How Browser Navigation Can Work in an SPA

### History API

* The History API has three methods, `pushState`, `replaceState`, and `go`
* `pushState` and `replaceState` reuse the same `Document` but updating the location
* The both take a `path` argument that can be a full URL, absolute URL, or relative URL - it just has to be within the same protocol and hostname
* `pushState` adds an entry to existing session history entries, but removes any entries ahead of the current entry
* `replaceState` replaces the current entry and does not affect any entries ahead of current entry
* `go(-1)` - go back one entry
* `go(1)` - go forward one entry
* `go(0)` - reload current page
* `pushState` and `replaceState` both take a `state` parameter
  * `state` is arbitrary data attached to an entry
  * It must be serializable (i.e can be turned into a string)
  * It often has a limit (640k characters in Firefox, for example)
  * Direct navigation has a state of `null` so this `state` value should not be relied upon for rendering purposes
  * `state` is maintained through navigation so navigating back to an entry with a populated `state` value means that the data is accessible

### Using the History API

* Override default link click behavior using `event.preventDefault`
* Then for the `onClick` callback, change the location using `pushState`
* Will probably need to let router know that location has changed and to load / execute any code for new route
* For back and forward navigation, both trigger a `popstate` event, so adding a listener for that `popstate` event and using History API accordingly - also will need to let router know of location change
* Since manual navigation creates new `Document`, and History API only prevents reloads for entries that share the same `Document`, navigating between these entries using back / forward / `go` will cause full re-renders
