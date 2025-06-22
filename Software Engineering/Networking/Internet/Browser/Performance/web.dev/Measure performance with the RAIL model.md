# [Measure performance with the RAIL model](https://web.dev/articles/rail#response-process-events-in-under-50ms)
* Users perceive smooth animations when 60+ new frames are rendered every second
  * 16ms per frame, including the time it takes the browser to repaint the new frame
  * Realistically leaves around 10 ms to produce a frame
* Respond to user actions within a 100ms time window for users to feel like the result is immediate
* 100-1000ms feels like a natural and continuous progression of "tasks" (like loading pages, changing viewis)
* 1000ms+ generally leads to users losing focus

## Goals
* Complete transitions initiated by user input within 100 ms
  * To ensure a visible response within 100 ms, process user input events within 50 ms
  * Clicking buttons, toggling form controls
  * Does not apply to touch drags or scrolls
* Animation: produce a frame in 10 ms
  * Visual animations (entrances, exits, tweens, loading indicators)
  * Scrolling, flinging, dragging
* A good target for first loads is to load the page and be interactive in 5 seconds or less on mid-range mobile devices with slow 3G connections
  * Subsequent loads should load the page in under 2 seconds
  * Mid-range Android phone (Moto G4) with a400 ms round trip time and 400 KBPS transfer speed
  * Don't have to load everything in under 5 seconds to produce the perception of a complete load (like lazy loading images, code-splitting, etc)

## DevTools
* Throttle CPU
* Throttle network
* View main thread
* Analyze frames per second
* CPU usage, JS heap size, DOM nodes, layouts per second

## Lighthouse
* Max Potential First Input Delay - estimates how long app will respond to user input based on main thread idle time
* Total Blocking Time - measure amount of time the page is blocked from responding to user input (mouse clicks, screen taps, keyboard press)
* Defer offscreen images
* Properly size images
* Avoid chaining critical requests
* Efficiently encode images
* Enable text compression
* Avoid enormous network payloads
* Avoid excessive DOM size
