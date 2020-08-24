# [Time-Saving CSS Techniques To Create Responsive Images](https://medium.com/free-code-camp/time-saving-css-techniques-to-create-responsive-images-ebb1e84f90d5)

* Can use `object-fit: cover` (just doesn't work for IE and older Safari, though a polyfill exists)
* `position: relative` + `position: absolute` image

```css
.wrapper {
  position: relative;
  padding-top: 56.25%; /* 16:9 aspect ratio */
}

img {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: auto;
}
```

* `picture` + `srcset` to change image source depending on page width (can make page load faster on mobile, for example, if a large hero image is not needed)

```css
<picture>
  <source media="(max-width: 799px)" srcset="some-image.jpg">
  <source media="(min-width: 800px)" srcset="some-larger-image.jpg">
  <img src="some-larger-image.jpg">
</picture>
```
