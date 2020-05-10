# [React Portals: What Are They And Why Should We Use Them](https://levelup.gitconnected.com/react-portals-what-are-they-and-why-should-we-use-them-7c082a62e8fa)

* In React, default behavior is to render the entire app under single DOM node the app `root`
* Sometimes, need to render children outside `root` DOM node like having a `z-index` to have the child visually appear on top of its container
* Usually the `root` is expressed as a `<div id="root"></div>`
* Can also define a `<div id="portal"></div>` in `index.html` file
* When component is mounting, add new element to `portal` root and when it unmounts remove component from `portal` root
* In `render`, create a portal using the `children` and the new `div` element that is appended to `portal` root in component mount
  * This uses `createPortal`, which takes two arguments
    * First, is any renderable React child
    * Second, is the DOM element that contains the child