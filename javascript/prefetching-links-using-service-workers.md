# [Prefetching Links Using Service Workers](https://blog.bitsrc.io/prefetching-links-using-service-workers-d9f6babfd0b)

* Check to see if the browser supports service workers = `'serviceWorker' in navigator`
* Then register the service worker by calling `navigator.serviceWorker.register('some/serviceWorker.js')`
* There are events that are fired associated with the installation and activation of the service worker
  * Activation is when the service worker starts interpreting requests and serving pages
  * Can also listen for when requests are made within the service worker (useful for responding to asset fetching when offline by returning from cache)
* Browser cache is temporary storage where responses are cached to avoid delay in fetching it when resource is requested again - have access to Cache Storage but not Browser Storage

```js
// Example of how to prefetch assets
self.addEventListener('install', function(event) {
    event.waitUntil(
        caches.open(cacheName).then((cache) => {
            //...
            return Promise.all(prefetchedURLs.map((url) => {
                return fetch(url).then(res => {
                    if(res.status >= 400) throw Error('request failed')
                    return cache.put(url,res)
                })
            }))
        }).catch((err) => {
            //...
        })
        //...
    )
});
```

* Should see cached assets in the `Cache Storage` section of the `Application` tab in dev tools
* However, still does not support offline serving of assets - this is because the service worker doesn't serve the files automatically
  * Service worker needs to listen to fetch requests for those assets and check if they exist in `Cache Storage`

  ```js
self.addEventListener('fetch', function(event) {
    event.respondWith(
        caches.match(event.request).then(function(res) {
            if (res) {
                l('Resource found in Cache Storage')
                return res;
            }
            return fetch(event.request).then(function(res) {
                return res;
            }).catch(function(err) {
                l(err);
            });
        }));
});
  ```
