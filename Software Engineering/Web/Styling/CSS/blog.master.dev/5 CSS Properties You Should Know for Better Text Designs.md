# [5 CSS Properties You Should Know for Better Text Designs](https://blog.master.dev/typographic-css-tricks/)

## `background-clip`
* Usually, words are filled by some solid color
* However, it's possible to use an image to fill a word as well

```css
p {
  /* `background-clip: text` included in the shorthand */
  background: text url("image.jpg") center/auto 1lh;
  /* transparent text so that the clipped background is visible underneath */
  color: transparent; 
}
```

## `align-content`
* `align-content` can be used without `grid` or `flexbox` and arranges the block box's content vertically

## `box-decoration-mode`
* Fragmentation is when content breaks along a flow, column, or page
* When this fragmentation occurs, the "broken edge" is typically unstyled
* `box-decoration-mode` is able to style these edges, like adding borders, shadows, etc

## `letter-spacing`
* CSS can only target the first glyph in a text
* `letter-spacing` determines the trailing space of all glyphs in a text
  * Can have both a "negative" and "positive" spacing (hide vs. show characters, respectively)

```html
<section id="text">
  <span>Ingvar</span> 
  <span>Kamprad</span> 
  <span>Elmtaryd</span> 
  <span>Agunnaryd</span>
</section>
```

```css
span {
  /* Shrink and hide the letters */
  letter-spacing: -1ch;
  color: transparent;
  /* Keep the first one visible */
  &::first-letter {
    color: #FBDA0C; /* yellow */
  }
  /* On expansion */
  body:has(:checked) & {
    /* Expand and show the letters */
    letter-spacing: 0ch;
    color: #0057AD; /* blue */
    transition: letter-spacing 0.4s cubic-bezier(.8, -.5, .2, 1.4), color 0.8s linear;
  }
}
```
