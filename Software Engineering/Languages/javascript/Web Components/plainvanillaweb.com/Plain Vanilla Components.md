# [Plain Vanilla Components](https://plainvanillaweb.com/pages/components.html)
* Extending elements other than the `HTMLElement` class doesn't work in Safari
* `connectedCallback` is called when the element is added to the DOM
  * Element is ready to make DOM updates
  * May be called multiple times when the element or one of its ancestors is moved around in the DOM
* Every web component must be registered via `window.customElements.define`
* There are custom element tag naming rules
  * Website chooses prefix of `x-`
* `observedAttributes` returns the element's attributes that when change cause `attributeChangedCallback` to be called
* Style using separate CSS file
  * Web components are HTML, so can style based on custom attributes (like the `size` attribute) using pure CSS
  * `<x-avatar src="some source" size="large"></x-avatar>`
* `script` tag that contains the web component JS can be moved `head` and add `defer` attribute
  * Download in parallel with rest of page, and execute JavaScript when the page is loaded
  * `script type="module"` enables ES module mode (i.e. `import` syntax) for all included JavaScript (supported by all modern browsers) without a transpiler
* Can compose web components like inserting web component as a child of another web component (example is an `x-avatar` web component that has an `x-badge` web component child)
  * Caseful to insert child _before_ any existing children that exist in the markup

## Shadow DOM
* HTMl `template`s are fragments of HTML that are easily cloned and appended to the DOM
  * For complex web components that have a lot of markup, the use of templates is often convenient
  * Instantiating the template outside the web component class means that it can be reused across instances of the web component
  * `template` fragment contains `<link rel="stylesheet" href="${import.meta.resolve('./header.css')}">`
    * `header.css` needs to be imported into the shadow DOM
    * `<slot></slot>` defines where child elements will be rendered
      * Only possible in web components that have a shadow DOM
  * Web component constructor is called when the element is first created, but before it's ready for DOM interaction
    * Constructor is guaranteed to be called exactly once, making it an ideal place to attach a shadow DOM
  * `this.shadowRoot.append(template.content.cloneNode(true));`
    * `shadowRoot` is the root element of the attached shadow DOM
    * HTML `template` is cloned and appended to the web component element's content
    * Shadow DOM is immediately available after `attachShadow` is called
* Styling the shadow DOM involves a `:host` pseudo-selector that appleis styles to the element from the light DOM that host the shadow DOM
  * Other styles are isolated inside the shadow DOM
  * Shadow DOM starts out unstyled, which is why there is a `reset.css` that is imported again
* Shadow DOM incurs a performance penalty, especially when putting hundreds of shadow DOMs on a page
  * Accessibility issues
  * Non-Google crawlers may have trouble
* Shadow DOM is recommended when styles and DOM need to be isolated from containing page
  * Web components designed to be used by others / embedded in third-party sites

## Passing Data
* Can pass data via events that are dispatched from child component to parent component
  * `preventDefault` when processing event, and then `target.reset` after dispatching the event
* Can pass data via class properties
  * Example is a `list` `set`ter that takes a new list and calls a custom component `update` method that re-renders the `innerHTML` of the component
  * Recommended way of passing complex data to statful web components
  * Need to propertly encode entities for complex data to avoid XSS
* Methods like `update(list)` are the recommended way of passing complex data to _stateless_ web components
  * `this.querySelector('stateless-component').update(this.#someList.slice())` vs. `this.querySelector('stateful-component').list = this.#list.slice()`
