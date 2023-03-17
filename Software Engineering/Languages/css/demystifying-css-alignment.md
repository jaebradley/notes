# [Demystifying CSS Alignment](https://medium.com/@patrickbrosset/demystifying-css-alignment-2d3ea7a02a36)

* Every web page has two directions - the inline axis and the block axis
  * Inline axis is the axis one which lines of text run on - by default it's horizontal
  * Block axis is the axis along which blocks are stacked - by default, it's vertical
* `justify` works along the inline axis
  * Need to "justify" text on a line (i.e. distribute words along a line)
* `align` works along block axis
* A container is a rectangle within which a subject is being aligned
* A subject is a thing or a set of things that are being aligned within the container
* `content` means that the property defines how subjects are aligned or distributed within a container
* `self` means that the property is applied to a subject within its container
* `items` is a way to set default values of `self` for all subjects within a container in one go
* `justify-content:center;`
  * Centering content within Flexbox container along inline axis
* `align-items:center;`
  * Setting the default self-alignment of items inside container along block axis

## Content distribution along block axis (`align-content`)

* Alignment container is the Flexbox container
* Alignment subject is the Flexbox line
* When there are multiple lines (like when content is wrapped), the Flexbox line spans from the top of the container to the bottom of the container
  * So then `align-content` will align items within the container appropriately across the multiple rows within the container
  * But `align-content` doesn't have any effect when the Flexbox container has one line because the Flexbox line is the same height as the container

## Self-alignment

* `justify-self` can't be used to align an item because there might be other items on the same line
* `align-self` can be used to align a given item (along block axis)
  * Alignment container is the flexbox line that item currently is in (in the case of wrapping)
  * Alignment subject is the item itself

