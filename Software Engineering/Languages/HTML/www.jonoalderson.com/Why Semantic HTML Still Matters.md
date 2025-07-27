# [Why Semantic HTML Still Matters](https://www.jonoalderson.com/conjecture/why-semantic-html-still-matters/)
* Tags like `<article>`, `<nav>` and `<section>` aren't just decorative - they express intent and hierarchy
* If everything is a `div` or `span` then nothing is meaningful
* Semantic HTML is the foundation of accessibility
* Parsing HTML isn't the bottleneck - CHromium can process HTML at 10 GB/s
  * Real cost is CSSOM construction, layout, paint, and composite
  * HTML parsing is blocking when you hit a non-deferred `<script>`
* Modern CSS workflows, especially in componentised systems, often lead to duplication
  * Each component declares its own styles, even when they repeat
* Every time a class rule toggles, the browse has to re-evaluate which rules apply where
* `contain: layout;` tells the browser it doesn't need to recalculate layout outside the element
* `will-change: transform;` hints that a compositing layer is needed
* `isolation: isolate;` and `contain: paint;` can help prevent visual spillover and force GPU layers
* `content-visibility: auto` lets the browser skip rendering elements that aren't visible on-screen
  * Requires predicatble layout, scroll anchoring
