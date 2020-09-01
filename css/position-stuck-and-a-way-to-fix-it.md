# [Position: stuck; - and a way to fix it](https://uxdesign.cc/position-stuck-96c9f55d9526)

* Situation is an overflowing table with a sticky header at the top of the page that also respect horizontal scrolling
* The issue with overflows + sticky is that a sticky element sticks to its nearest ancestor that has a scrolling mechanism (created when overflow is hidden, scroll, auto, or overlay) even if that ancestor isn't the nearest actually scrolling ancestor
  * The headers don't stick after you vertically scroll past the table
* By specifying a height on the overflowing container, this makes the headers stick, but only scrolling inside the container

## Solution

* Headers are separated into their own `div` that is `position: sticky`
  * An inner `div` called `scroller` containers the header content so that the content can scroll horizontally (`overflow: auto`)
* To sync horizontal scrolling between the header and the columns, need to do this via some JS (no pure CSS solution)
* To prevent a horizontal scrollbar, set `overflow-x: hidden` on the `scroller`
