# [Inversion Of Control](https://kentcdodds.com/blog/inversion-of-control)

* General pattern of building reusable code (function, component, hook), need new use case, add argument / prop / option (many times), and then reusable code is harder to maintain
  * Each branch of logic in code compounds with existing branches of logic - need to support potential combinations of args / props / options that nobody is using but have to make sure not to break as new features are added
  * Adds to documentation complexity
* Inversion of control is making abstraction do less stuff and making users do it instead
* Assume `Array.filter` does not exist and creating a custom `filter` function
  * Let's say there are a bunch of new use-cases like filtering `null`s or `undefined`s or `0`s and these use-cases are expressed through function arguments
  * Instead of providing all these arguments, instead provide a filter function as an argument - this filter function is where the inversion of control occurs
  * The responsibility of deciding which element gets in the new array from the filter function is shifted from the filter function to the person calling the filter function
* People will argue that the API that has inversion of control is harder to use (and they might be right)
  * But can implement easier to-use APIs on top of those APIs

```javascript
function filterWithOptions(
  values,
  {
    filterNull = true,
    filterUndefined = true
  } = {}
) {
  return filter(
    values,
    value => !(
      (filterNull && value === null) ||
      (filterUndefined && value === undefined)
    )
  )
}
```

## Compound Components

* Building a `Menu` that has `items` that perform some action on click (and have other attributes)
* Can have an `items` `prop` but doesn't allow a lot of flexibility

```jsx
<Menu
  buttonContents={
    <>
      Actions <span aria-hidden>▾</span>
    </>
  }
  items={[
    {contents: 'Download', onSelect: () => alert('Download')},
    {contents: 'Create a Copy', onSelect: () => alert('Create a Copy')},
    {contents: 'Delete', onSelect: () => alert('Delete')},
  ]}
/>
```

* Could invert control here and let the developer building this menu decide how things should be rendered

```jsx
<Menu>
  <MenuButton>
    Actions <span aria-hidden>▾</span>
  </MenuButton>
  <MenuList>
    <MenuItem onSelect={() => alert('Download')}>Download</MenuItem>
    <MenuItem onSelect={() => alert('Copy')}>Create a Copy</MenuItem>
    <MenuItem onSelect={() => alert('Delete')}>Delete</MenuItem>
  </MenuList>
</Menu>
```