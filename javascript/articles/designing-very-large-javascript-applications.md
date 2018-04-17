# [Designing Very Large JavaScript Applications](https://medium.com/@cramforce/designing-very-large-javascript-applications-6e013a3291a3)

* What being a senior engineer means is that I'd be able to solve almost every problem that somebody might throw at me
  * I know my tools, I know my domain
  * I make junior engineers into senior engineers
* I know how I would solve the problem => I know how others would solve the problem => I can anticipate how API choices and abstractions impact the way other people would solve the problem
* Programming model = "given a set of APIs / libraries / frameworks / tools - how do people write software?"
* What code splitting means is that you define a set of bundles for your application
  * Some users only use this part of my app, some other users use another part so only put together bundles that only get downloaded when the part of an application that a user is actually dealing with is executed
  * Impacts programming model because now you need a particular so you need to go to the network and application becomes more complex
* Route-based code splitting works at decent scale but what about really big scale like Google (where you can google for articles on public speaking or the weather or a calculator) all with the same route? The route-based bundle would be big (few megabytes) and users would get unhappy
* What about lazy-loading all components?
  * ES6 allows for lazy-loading modules
  * Impacts programming model because how have to remember who lazy-loads what, when?
  * And how to pick between static and dynamic import
  * One static import, when it should have been dynamic, suddenly may put stuff into the same bundle that it shouldn't be - this *will* happen given enough engineers and enough time
  * Google splits components into rendering logic and application logic
    * Only load application logic for component when it was previously rendered
    * Server-side render a page, whatever is actually rendered triggers downloading associated application bundles
  * React and Vue.js use a process called hydration
    * Server-side render something
    * Client re-renders it
    * Load code to render something that is already on the page, which is wasteful.
    * Nice because you get to ignore on the client-side that you server-side rendered something
* "Code that is no longer needed but still in codebase"
  * CSS is particularly famous for this
  * One big CSS file
    * Need to delete this selector - do we really know if it matches anything in app?
    * So it lives in app forever
  * CSS-in-JS means that when you delete component, you delete everything associated with that component (like the CSS)
  * webpack.config.js
    * One thing that is assumed to build entire app
    * Eventually needing to know about every aspect of what some other team did somewhere in the app doesn't scale
* "Base bundle pile of trash"
  * The base bundle in your graph of application bundles is the one that will always get loaded
  * Important for this to be as small as possible because dependent bundles at least have a shot at being small
  * Google Plus JS base bundle was 800kb
  * Base bundle should not have any form of UI code, because depending on how a user enters app, there might be different UI
  * Forbidden dependency tests
    * Test failure if React.Component is not a transitive dependency of base bundle
  * Tests are not only for testing that your math functions do the right thing - they are also for infrastructure and for the major design features of your application
* Not every engineer in your organization can and will understand how code splitting works - and they don't need to do that
  * Try to introduce these things into your application in a way that is fine when not everybody understands them
* And really just make it easy to delete code
