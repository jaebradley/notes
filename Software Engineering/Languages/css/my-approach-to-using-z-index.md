# [My Approach To Using Z-Index](https://medium.com/hackernoon/my-approach-to-using-z-index-eca67feb079c)

* `z-index` applies to positioned elements - so need to add `position: relative` when using
* Group elements that use `z-index` into a local or global category
  * A dropdown menu is global
  * A full-screen modal is global
  * A header could be global (author says it could go either way)
  * A product item list on a page is local
* For global z-indexes, since we care about how two elements stack relative to each other when they're both on the screen at the same time record the `z-index`es in the same place for each of the elements
  * "Global" `z-index`es really means setting `z-index` on elements in the root stacking context
  * Like in the same `zindexes.scss` file with Sass variables for each "global" `z-index` value
  * Shouldn't need that many global `z-index`es as there should only really be one drop-down menu / tool-tip / full-screen modal at a time
  * Author uses `1, 2, 3...` for the `z-index` value - there argument for using `10, 20, 30...` is that if there needs to be a `z-index` in-between two values it's easy to use `35` (for example) vs. shifting everything else up by 1
* A "local" `z-index` means setting the `z-index` of an element within a new stacking context below the root stacking context
  * When an element creates a new stacking context, no child element will be able to render on top of elements elsewhere on the page
  * This "scopes" `z-indexes` turning a "local" `z-index` of `999` into `0.999` when compared to the "global" `z-index`es
  * So in the case of the product list, the list should never render above elements outside the product list - so it can be safely scoped by creating a new stacking context for the product list

  ## Creating New Stacking Contexts

  * Any positioned element (including relatively positioned elements having a `z-index` value other than `auto`)
    * Explain why you're doing this / creating a new stacking context (or add a mixin with this name)

```scss
@mixin new-stacking-context {
  position: relative;
  z-index: 0;
}

.product-list {
  @include new-stacking-context;
}
```

* Can also create a new stacking context using `transform` with any value other than `none` (`transform: translate(0)`)
  * `position: fixed` is not relative to the viewport - any value other than `none` for transform will cause elements to become a containing block for fixed positioned descendants
  * So if you do `transform: translate(0)` to create a new stacking context, no child can ever be fixed relative to the viewport
* Can also create a new stacking context by defining an opacity less than `1` (but this is not a good idea)