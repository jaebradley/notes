# [Build Your Own Router](https://jschof.dev/posts/2025/11/build-your-own-router/)
* `URLPattern` is available in all browsers
* `URLPattern` can be used to test if a URL matches a pattern
* `new URLPattern({ pathname: "/cat{/}?" })` will match `/cat` and `/cat/` but not `/cat/dog`
* `new URLPattern({ pathname: "/cat{/}?"* })` will match the above examples as well as `/cat/dog?some=query`
* Router config is an array of `URLPattern`s that map to a component to render
  * Should be a private variable to avoid attackers from overwriting this config via the browser console
  * Generally speaking, never render based off of query parameters or dynamic segments of the URL
  * Keep the actual rendering web components as a static (and private) list / mapping
* Iterate over each of the `URLPattern`s and see if they match the current `window.location.href`
* For single page web applications, need to override the default link click behavior

```javascript
class MyRouter extends HTMLElement {
  connectedCallback() {
    window.addEventListener("click", this.handleClicks);
  }

  handleClicks = (event) => {
    if (event.target instanceof HTMLAnchorElement) {
      // Don't handle this link like usual!
      event.preventDefault();

      // Set the URL manually
      const toUrl = event.target.getAttribute("href");
      window.history.pushState({}, "", toUrl);

      // Now that the URL is set, do the usual matching
      // and rendering!
      const matchedComponent = this.getRouteMatch();
      this.renderComponent(matchedComponent);
    }
  };

  disconnectedCallback() {
    window.removeEventListener("click", this.handleClicks);
  }
}
```

* `pushState` will add a new entry to the browser navigation history
* When a browser navigates forward/backward in its navigation history, the `popstate` event is emitted
* In the case of the single page web application, listen to this event and render the relevant component

```javascript
connectedCallback() {
  window.addEventListener("click", this.handleClicks);
  window.addEventListener("popstate", this.handlePopState);
}

handlePopState = (event) => {
  const matchedComponent = this.getRouteMatch();
  this.renderComponent(matchedComponent);
};

disconnectedCallback() {
  window.removeEventListener("click", this.handleClicks);
  window.removeEventListener("popstate", this.handlePopState);
}
```
