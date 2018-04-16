# [Performance Turning a React Application](https://codeburst.io/performance-tuning-a-react-application-f480f46dc1a2)

* Tello web application (~10k lines)
  * React/Redux
  * Webpack
  * Node backend (Express + MongoDB)
* On 3G, ~5 seconds until first paint, ~15 seconds for all network requests to resolve
* Initial timeline
  * Initial HTML page fetch is fast (app is not server-side rendered)
  * Monolithic JS bundle takes a long time
  * React initially renders
  * Application makes request for user data
  * Once user data is available then application fetches images and episodes for each show
* Adding `compression` module for `526kb` bundle decreased it to `156kb` which cut time to first paint from 5 seconds to 3 seconds
* Even though first paint is at 2400ms, it isn't meaningful since episode data isn't received until about 5 seconds
  * Server-side rendering doesn't fix problem because server has to make call to DB and then call the TV Maze API
  * User still sees white screen
  * Uses local storage to persist state changes. Stale data, but initial load will feel much faster
  * Doesn't affect load time of new data, but user gets stale data immediately
  * Downside is that user doesn't know that there's still more data to come - add spinner to show user
* Use react-lazyload for lazy loading episode images
* Minor performance gains from code-splitting mobile from web
* Minor performance loss when CDN hadn't cached react and react-dom CDN dependencies
