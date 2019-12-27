# [How Single-Page Applications Work](https://blog.pshrmn.com/how-single-page-applications-work/)

* A SPA is a website that re-renders its content in response to navigation actions without making a request to a server to fetch new HTML
* SPAs normally depend on a router, which is comprised of routes that describe a set of matching locations
  * Once a matching route is identified, router will trigger re-render of application

## Browsing Context

* Every tab has session history, which is an array of location entries
  * Each location entry has information about its location like the location's URL, associated Document object, etc.
* Browser creates the Document object based on the response from the server
  * Document object describes the page / DOM and provides API for interacting with it
* Each time user navigates through browser (via clicking links) a new entry is created in session history array
* When a user presses the back button, the browser uses the previous entry in the array as the current entry and loads the Document object from that previous entry
  * The current (soon-to-be "previous") entry is still a part of the session history array, but the pointer of the current entry is referencing the previous index in the session history array
* Clicking a link will drop all entries after the current entry unless that link navigates to the exact same location as the current location

## History API

* Instead of creating new Document object for every location, History API re-uses the active Document object, but modifies certain properties, like the updated location
* `pushState` adds entry to session history after current entry
  * If there are entries after current entry, those are lost when pushing a new location (like session history after clicking a link)
* `replaceState` replaces current entry in session history - any entries after current entry are unaffected
  * Can replace current entry with _any_ location
* Use click handler to override default behavior using `event.preventDefault`
  * Then have click handler call `pushState` / `replaceState` to perform navigation without triggering a server request
  * History API only updates session history, so click handler needs to talk to router to let it know about new location
* To detect back/forward button presses, use `popstate` event listener
* If user updates location manually using address bar, navigation will create new Document
  * History API only prevents reloads with entries that share the same Document
  * Clicking forward/back buttons to navigate between entries will cause full-page reloads
