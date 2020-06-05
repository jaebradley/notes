# [Recoil - Another React State Management Library](https://medium.com/swlh/recoil-another-react-state-management-library-97fc979a8d2b)

* Redux or Mobx don't have access to React's inner scheduler which means that they won't be easily able to support React concurrency mode
* Redux has some boilerplate cost associated with it to set up even a basic store
* Issue with Context API is that it doesn't let client subscribe to subset of data
  * If provider's value changes, it will cause everything subscribed to that context to re-render

## Recoil Basics

* Atom is a piece of state - like regular component state, but that any component can subscribe to
  * Changing the value of an atom will result in a re-render from all components subscribed to it
  * `useRecoilState` is like `useState` - let's you subscribe to atom value and update it
  * `useRecoilValue` just returns the value of the atom - no setter
  * `useSetRecoilState` - just the setter function
  * An atom's key must be unique across the application
  * The atom's default value can be static, or a function (async or synchronous)
  * This default value is initialized the first time any component calls the atom
* Atom Family is the same as a regular atom, but it can receive a parameter that differentiates one instance from another

```jsx
const getImage = async id => {
  return new Promise(resolve => {
    const url = `http://someplace.com/${id}.png`;
    let image = new Image();
    image.onload = () =>
      resolve({
        id,
        name: `Image ${id}`,
        url,
        metadata: {
          width: `${image.width}px`,
          height: `${image.height}px`
        }
      });
    image.src = url;
  });
};
export const imageState = atomFamily({
  key: "imageState",
  default: async id => getImage(id)
});

// Images list
const Images = () => {
  const imageList = useRecoilValue(imageListState);
  return (
    <div className="images">
      {imageList.map(id => (
        <Suspense key={id} fallback="Loading...">
          <Image id={id} />
        </Suspense>
      ))}
    </div>
  );
};
// Single image
const Image = ({ id }) => {
  const { name, url } = useRecoilValue(imageState(id));
  return (
    <div className="image">
      <div className="name">{name}</div>
      <img src={url} alt={name} />
    </div>
  );
};
```

* Selectors are derived slices of state - they allow setter functions that can modify state

```jsx
const boxState = atomFamily({
  key: "boxState",
  default: COLORS.WHITE
});

const colorCounterState = selector({
  key: "colorCounterState",
  get: ({ get }) => {
    let counter = { [COLORS.RED]: 0, [COLORS.BLUE]: 0, [COLORS.WHITE]: 0 };
    for (let i = 0; i < BOX_NUM; i++) {
      const box = get(boxState(i));
      counter[box] = counter[box] + 1;
    }
    return counter;
  },
  set: ({ set }) => {
    for (let i = 0; i < BOX_NUM; i++) {
      set(boxState(i), COLORS.WHITE);
    }
  }
});
```