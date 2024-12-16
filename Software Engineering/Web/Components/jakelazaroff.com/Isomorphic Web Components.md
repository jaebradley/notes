# [Isomorphic Web Components](https://jakelazaroff.com/words/isomorphic-web-components/)

## The Current Landscape

* Custom elements let you attach logic to specific points in the DOM
* Can declare these attachment points in a HTML markup
* CSS web components utilize these custom element attributes for CSS selectors
* What people usually mean when they say "Web Components" is adding client-side behavior to custom elements by subclassing `HTMLElement`s
  * And then "server-side rendering Web Components" means generating the client-side markup on the server-side
  * The approach of executing the same logic in two different environments (client vs. server) is called isomorphism
* Benefit of writing a web component vs. a Svelte component is to work directly with the web platform
  * No need to add a build step or use a custom API / framework
* Web components act as a decoupling layer
  * Doesn't matter how the component was built (or using XYZ library), the component can be referenced in a Svelte app, Astro, Markdown, HTML and it will "Just Work"

## Isomorphic Rendering

* Author's goal is to write a "pure" web component and server-side render it
* Emulate DOM using Happy DOM
* Import `render` function before the web components are imported so that when the web components are declared, the browser APIs they rely on are already declared in Node's global scope
* For shadow DOM, there is a `serialzable` property that instructs the element to render the shadow root into HTML
* Author is then able to add a custom component that has light DOM markup and shadow DOM markup without any special tooling
