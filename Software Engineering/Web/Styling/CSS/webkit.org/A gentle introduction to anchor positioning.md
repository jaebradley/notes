# [A gentle introduction to anchor positioning](https://webkit.org/blog/17240/a-gentle-introduction-to-anchor-positioning/)
* Anchor positioning: place an element on a page based on where another element is
* Example: click on an avatar image and you want a menu to appear below it
  * Clicking interaction can be handled via CSS using the Popover API
  * Usually requires CSS, but can be accomplished with CSS as well
* Mental model is to think of the menu as if it was anchored to the avatar
  * Avatar = anchor
  * Menu = target

```css
.profile-button {
  anchor-name: --profile-button;
}

.profile-menu {
  position-anchor: --profile-button;
  position: absolute;
  position-area: block-end span-inline-end;
}
```

* `position-area` places element on a nine-square grid
  * Anchor takes the center spot
  * `block-end span-inline-end` will start position directly below the avatar and spill over to the end of the inline direction

https://webkit.org/wp-content/uploads/Image-32.png<img width="946" height="864" alt="image" src="https://github.com/user-attachments/assets/1e42c55c-3b4b-4c5c-acbf-c63f3466a40c" />

https://webkit.org/wp-content/uploads/Image-36.png<img width="1442" height="620" alt="image" src="https://github.com/user-attachments/assets/eebb5d28-34d9-4ab1-b6af-977670b8c9c1" />

* To handle responsiveness, probably better to right-align and let menu spill over to left instead
  * Can tell menu to switch to a different position when it runs out of room
  * `position-try: block-end span-linline-start`
* `position-area` uses a grid to place the target while `anchor` is about placing target based on edges of the anchor
* `anchor` can only be used with inset properties (`top`, `right`, `bottom`, and `left`)
  * `left: anchor(left)` or `top: anchor(bottom)` or `inset-inline-start: anchor(start)` or `inset-block-start: anchor(end)`
* To add some padding, can use `anchor` with the `calc` function
  * `inset-inline-start: calc(anchor(start) +1 .25em)`

