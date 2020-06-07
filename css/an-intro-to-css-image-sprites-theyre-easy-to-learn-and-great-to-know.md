# [An Intro To CSS Image Sprites: They're Easy To Learn And Great To Know](https://medium.com/free-code-camp/an-intro-to-css-image-sprites-theyre-easy-to-learn-and-great-to-know-c13beec82403)

* Instead of making a request for your profile image, friend's image, album thumbnail, sprites allow you to use one image, which means only one request

```html
<div class="main" id="main">
  <div id="first"></div>
   <div id="second"></div>
   <div id="third"></div>
  <!-- Defined three divs that will hold the image as background -->
</div>
```

```css
#first{
  background: url("https://i.postimg.cc/R0N7nkH9/flags.png") -86px -87px ;
  width:90px;
  height:90px;
  background-size:1400px;
  margin:5px 5px;
}
```

* The image is scaled to `1400px` - this allows us to zoom in on an image until it's the only part that's showing
* the `-86px -87px` means that image will be shown that's offset by 87 pixels from the top and 86 pixels from the left
  * First number is displacement on x-axis (negative means left to right and positive means right to left)
  * Second number is displacement on y-axis (negative means top to bottom and positive means bottom to top)
* In the flag image that's used in example, each flag is `87px` in size - to get flag next to it, need to offset by `89px` due to size plus white space (`1px` on left and right)