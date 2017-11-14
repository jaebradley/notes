# [Why Is My Create React App Seeing `404`s When Deployed on Heroku?](https://www.darraghoriordan.com/2017/04/30/create-react-app-react-router-and-heroku-the-404-gotcha/)

Create a React app using the [Create React App](https://github.com/facebookincubator/create-react-app) plugin and use React Router to map particular routes to particular views.

When testing the app locally, the routes produce the expected views. However, after deploying to
Heroku, the same routes result in `404`s.

The solution is to add a `static.json` file in the root directory of your project with the following content

```json
{
  "root": "build/",
  "clean_urls": false,
  "routes": {
    "/**": "index.html"
  }
}
```

The reason (from [StackOverflow](https://stackoverflow.com/a/42866874/5225575))

> You need to modify Heroku's default webpack, otherwise the service gets confused with how to handle the client-side routing. Essentially what static.json does. The rest is just the correct way to handle the routing according to the 'react-router' documentation.

Supplemental documentation from the [`Create React App Buildpack README`](https://github.com/mars/create-react-app-buildpack#routing-clean-urls).
