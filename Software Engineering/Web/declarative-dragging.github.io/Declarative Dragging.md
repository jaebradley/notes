# [Declarative Dragging](https://declarative-dragging.github.io)
* Calculate all the future application states if the dragged element was dragged to a specific location
* Speculatively render those states so that the drag-and-drop interaction is smooth
* D2 library starts with some model state

```json
{
  "rows": [
    ["A", "B"],
    ["X", "Y"]
  ]
}
```

* A render function maps the model state to some view tree
* When a drag starts, each of the possible target states are computed

```json
// "A" is dragged down to the bottom row
{
  "rows": [
    ["B"],
    ["A", "X", "Y"]
  ]
}

// "A" is dragged down to the bottom row as the middle element
{
  "rows": [
    ["B"],
    ["X", "A", "Y"]
  ]
}

// "A" is dragged down to the bottom row as the last element
{
  "rows": [
    ["B"],
    ["X", "Y", "A"]
  ]
}

// "A" is dragged to the right of "B"
{
  "rows": [
    ["B", "A"],
    ["X", "Y"]
  ]
}
```