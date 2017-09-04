# React Router With A Default

[`Switch` renders the first matched component](https://github.com/ReactTraining/react-router/blob/master/packages/react-router/docs/api/Switch.md).

```javascript
<Switch>
  <Route exact path="/about" component={About} />
  <Route exact path="/projects" component={Projects} />
  <Redirect to="/about" />
</Switch>
```

Tries to match the `about` path to the `About` component, and the `projects`
path to the `Projects` component. Any path that doesn't match gets redirected
to the `about` path.
