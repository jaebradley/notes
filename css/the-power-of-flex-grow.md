# [The Power Of `flex-grow`](https://medium.com/hackernoon/the-power-of-flex-grow-d8ea61ccf16e)

* Three approaches to having three buttons evenly spaced horizontally across a section of the page
  * Hard-code the lengths to `100px` or something
  * Use % width (so roughly `33%` each)
  * Use `flex-grow` and `flex-basis`
    * `flex-basis` is similar to `min-width` - it basically sets a base width for content
* So three buttons with `100px` widths, `flex-basis: 0`, and `flex-grow: 1.5` means that if any of the three buttons were removed, the two remaining buttons would adjust their width to account for extra space, using `100px` as basis for adjusted width
* So if a button was removed from the first or second approaches, it would basically look like two buttons that haven't taken up any of the remaining space - a `flex-grow: 0` would also have the same behavior
* Changing the `flex-grow` of each component individually would mean that they would grow relative to their `flex-grow` values (so if first element was `1.2` and second element was `1.7` the first element would be smaller than the second element)
* Adding an additional element would force elements off the screen for the first approach (fixed `px` width), and would force an element to overlap with another for the second approach (`%` width)
* With `flex-grow` the space would adjust for new element (with many new elements added, taking advantage of a row wrap feature would be best)
* Another way to apply `flex-grow` is applying `flex-grow` on hover state
  * Can do this with `css transform` but the difference is that while the size of the active button may increase, the size of all other buttons are decreased so the total amount of space is the same
* Imagine a column list of buttons and on click of a specific button, some content is shown
  * When this content is shown, the `flex-grow` of the content container is larger than all the other elements so that it takes up most of the space while the other content shrinks