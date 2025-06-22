# [Critical rendering path](https://developer.mozilla.org/en-US/docs/Web/Performance/Guides/Critical_rendering_path)
* Document object model is created as HTML is parsed
* HTML may request JavaSCript, which may alter the DOM
* HTML has or makes requests for styles which builds the CSS object model
* Browser engine combines the DOM + CSS object model to create the render tree

## Understanding CRP
* Browser initiates requests every time it finds links to external resources (stylesheets, scripts, embedded image references)
* Some requests are blocking = parsing the rest of the HTML is halted until the imported asset is handled
* After building the DOM, the browser constructs the CSS object model
* Browser uses the DOM + CSSOM to build the render tree, computing styles for the visible content
* After the render tree is complete, layout occurs, defining the location and size of all the render tree elements
* Page is then rendered (painted) on the screen

### CSS Object Model
* While DOM construction is incremental (built parsed node at a time) CSS is render blocking
* Browser blocks page rendering until it receives and processes all CSS
* CSS is render blocking because rules can be overwritten so content can't be rendered until the CSS object model is complete
* While CSS "cascades", descendant nodes may overrides rules from parent nodes
* So while the CSS object model gets built as the CSS is parsed, the object model can't be used to build the render tree until all the CSS has been completely parsed
* Less-specific selectors are faster than more specific ones
* `.foo` is faster than `.bar .foo` because the browser has to walk up the DOM to check if `.foo` is an ancestor of `.bar`
  * Penalty is not worth optimizing around

## Layout
* Layout is dependent on the render tree being built and the size of the screen
* Layout performance is impacted by the DOM - the greater the number of nodes, the longer layout takes
* Layout can be become a bottleneck, leading to jank if required during scrolling or other animations
* Any time the render tree is modified (added nodes, altered content) layout occurs
* To reduce frequency and duration of layout events, batch updates

## Paint
* On initial load, the entire screen is painted
* After that, only impacted areas of the screen will be repainted
* Browsers are optimized to repaint the minimum area required
* When measuring an animation frame, take into account layout and re-paint times
