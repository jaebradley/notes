# [Layout In CSS](https://medium.com/crowdbotics/layout-in-css-634c3ca3dcff)

* Benefit of components is that they are reusable and can be placed anywhere on a website and still look right
* Padding is part of the element - things like `border` and `background` depend on it
* Margin is the minimum empty space away from an element
* It's important that you refactor things into variables which semantically mean the same thing vs. they happen to have the same value
  * Refactor things that have both the same value _and_ the same meaning
* Variables can include things like spacings, borders, font-sizes, colors, shadows, etc

## Positioning Components Within Containers 

* Decoupled containers and components where components within a container don't care about their positioning
  * The container is in charge of positioning the components it contains - so containers are in charge of spacing components away from each other properly
  * Components can fill up entire area that the container provides
  * If a component never cares about where it's positioned, it is completely decoupled from its container
  * Theoretically, any component or container can be swapped out at any time and everything will still look perfect
  * By contrast, if a component needs to change its `margin` depending on where it is and what's around it, then it will never be completely reusable
* Using `grid` to achieve decoupled containers / components
  * Has `gutters` via the `gap` property to position child components without needing a `margin`
  * Can get messy if using `grid-template-columns` or `grid-template-rows`
  * To use `grid-template-areas`, the child components will need to have a complimentary property set on them
* Using `flex` or `block` to achieve decoupled containers / components
  * If there are multiple components inside a container, they would need a `margin` to space themselves from their siblings
  * For those components to fill up the container without any margins, there would need to be an inner container that will have negative `margin` to counteract margin of the components so that components will fill up the entire space provided by the initial container
  * If using `flex`, it generally doesn't require `margin` though, so above concern is not as valid
  * Will require an additional inner container
  * Components need to set their own margin
* Container strongly selects components
  * This increases specificity which is apparently a very sub-optimal practice

```css
.container {
  .component { margin: 1rem }
  .component:last-child { margin: 2rem }
}
```
