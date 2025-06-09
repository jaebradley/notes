# [Progressive JSON](https://overreacted.io/progressive-json/)

## Streaming JSON
* Streaming JSON parser would produce an object tree from incomplete input
* However, incomplete data can lead to "malformed" content
  * Missing property might not actually be "missing", but hasn't appeared in the streamed JSON document yet
* Parallels between a streaming JSON parser and streaming HTML
* Browser can display partial content as it is streamed, but one slow part of a JSON document or an HTML document can delay all other subsequent pieces of data

## Progressive JSON
* Send data breadth-first

```json
{
  header: "$1",
  post: "$2",
  footer: "$3"
}
```
* $-prefixed fields are references to data that hasn't been sent yet
* Pieces of data would be `Promise`d like

```json
{
  header: "foo",
  post: new Promise(/* ... not yet resolved ... */),
  footer: "bar"
}
```

* Can also send certain placeholder data in multple chunks
* Example is if both getting `post` information as well as `comments` information for a `post` are both slow
* So `post -> { content: "some content", comments: new Promise() }`
* And then `post.comments -> ["first comment", "second comment"]`

## Streaming Data vs. Streaming UI
* This progressive content "filling" is how React Server Components work

```react
# Start
<html>
  <body>
    <header>Welcome to my blog</header>
    {new Promise(/* ... not resolved yet */)}
    <footer>Hope you like it</footer>
  </body>
</html>

# Then
<html>
  <body>
    <header>Welcome to my blog</header>
    <article>
      <p>This is my post</p>
      {new Promise(/* ... not resolved yet */)}
    </article>
    <footer>Hope you like it</footer>
  </body>
</html>

# Finally
<html>
  <body>
    <header>Welcome to my blog</header>
    <article>
      <p>This is my post</p>
      <ul>
        <li key="1">This is the first comment</li>
        <li key="2">This is the second comment</li>
        <li key="3">This is the third comment</li>
      </ul>
    </article>
    <footer>Hope you like it</footer>
  </body>
</html>
```

* React doesn't display content "holes" for loading Promises
* React displays loading state via `Suspense` boundaries
