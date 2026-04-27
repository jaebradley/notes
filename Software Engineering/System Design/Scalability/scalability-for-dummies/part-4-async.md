# Part 4: Async

* Starting analogy: Go to bakery, ask for bread, but they are out - gotta come back in 2 hours when ordered bread is ready

## Async #1

* Bake bread at night and sell in morning
* Do time-consuming work in advance and serve finished work with low request time
* This usually means turning dynamic content into static content
  * Prerendering and locally storing pages of website as static HTML
  * Maybe called by cron job
  * Can make websites scalable and performant
  * If cron job uploads pre-rendered HTML pages to AWS S3 or CDN then website can be very performant

## Async #2

* When customer has special requests that cannot be forseen / are very specific then use queueing system to handle requests asynchronously
* User comes to website and starts computational intensive task
  * UI sends job request to backend and signals to user that task is being processed and they will be notified when task is done
  * UI is constantly checking to see if job is done - when queue / worker finishes processing job, UI will see this and inform user.
