# [Building A Responsive Image](https://medium.com/9elements/building-a-responsive-image-e4c6229fa1f6)

* When you use media queries inside SVG files and then insert the image via an `img` tag or as a CSS background-image the media queries are bound to the width of the image
* In the author's use-case the logo consists of a visual element and the company name - when the logo is small, the name is not displayed
* When creating the SVG, there is no `viewBox` attribute, only `width` and `height` (set to `100%`)
* Use `SVGOMG` to reduce file size and delete unnecessary SVG attributes - do _NOT_ clean IDs as these are used by Sketch
* Add symbol tags to final file and give each a unique ID as well as a viewBox attribute
  * Inside the `symbol` tags, paste exported content - in author's case there are two symbols to match the company image and the company name text
* To use the symbols need to use the `use` tag
  * `<use xlink:href="ix" x="0" y="0" width="100" height="100" />`
  * The `xlink:href` will point to the symbol with that id (so the company image) and render it
  * Notice that the `width` and `height` don't have units - these would be defined by the `viewBox` attribute on the `SVG` tag
    * But since one was not set, and only the width and height were defined (as 100%) one unit = one pixel - even if the width of the SVG was changed
    * Can also use percentages when defining the `xlink:href`
* When both the image and the company name should be visible, need to create a new symbol that encompasses both the image symbol and the company name symbol
  * This symbol has it's own `viewBox` attribute, which allows for the other symbols to be placed inside this new coordinate system

```css
<symbol id=”portrait” viewBox=”0 0 160 180">
  <use xlink:href=”#ix” x=”40" y=”32" width=”80" height=”80"/>
  <use xlink:href=”#typo” x=”3" y=”130" width=”154" height=”16"/>
</symbol>
```

* These positions come from sketch file that show the distances between the logo and the name
* To scale down this symbol so it's not 100% width, can do `<use xlink:href="#portrait" x="5%' y="5%" width="90%" height="90%" />`

## Making Everything Responsive

* Add `classes` to the `use` tags - one `class` for the "square" (i.e. when tiny / not showing the company name) and for "portrait" (i.e. when larger and shows both the logo and company name)
  * Use `style` tag in SVG + media queries
  * Instead of media queries using `min-width` use aspect ratio - this is so that when the image changes from portrait mode to landscape, only show the "square" version and not the one that contains the company name

```css
<style>
  .square { visibility: hidden; }
  .portrait { visibility: visible; }
  @media (min-aspect-ratio: 1/1) {
    .square { visibility: visible; }
    .portrait { visibility: hidden; }
  }
</style>
```
