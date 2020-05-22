# [Pass Multiple Children To A React Component With Slots](https://daveceddia.com/pluggable-slots-in-react-components/)

* Use concept of "slots" when component has multiple children and needs to place them in the layout as it sees fit
* Create props for these "slots" (like a `left` prop for content that needs to render on the left side of a `NavBar`) and pass these props `JSX`

```jsx
<Layout 
  left={<Sidebar/>}
  top={<NavBar/>}
  center={<Content/>}
/>

function Layout(props) {
  return (
    <div>
      <div>{props.top}</div>
      <div>{props.left}</div>
      <div>{props.center}</div>
    </div>
  )
}
```

* A nice bonus of using `children` prop or these "slot" props is that when the prop is defined it is in the scope of the parent - this can avoid a lot of prop-drilling potentially by removing one level of prop passing
