# [My Awesome React/Redux Structure](https://medium.com/better-programming/my-awesome-react-redux-structure-6044e5007e22)

* Application lifecycle
  * View dispatches Action
  * Action calls Effect
  * Effect makes API calls and sanitizes incoming data / returns error
  * Action receives models or error and dispatches it
  * Reducer receives models or error and updates store appropriately
  * Selectors take Reducers data and creates specific data for Views
  * Views get data from store / selector and displays it
* Proposed file structure
  * `constants` - for static data that doesn't change
  * `environments` - static info that might change across different environments
  * `models` - shared models and interfaces
  * `selectors` - business logic, models / interfaces generated for views
  * `stores` - contains many sub-directories, each of which seem to have a constant format
    * Each direct sub-directory represents a feature / API domain
    * `models` - API-related models
    * `actions` - Action creators
    * `effects` - API calls / response data sanitization
    * `reducers` - Handles actions and updating global store
  * `utilities` - helper code
  * `views` - View components
* Views - all views should do is display data and should be free from logic that _transforms_ data
* As long as the components are _not_ going to be reused, prefer to directly connect components rather than have parent get data and pass to props
* All actions should be flux standard actions
  * `type` - unique string to identify to consumers the intent of action
  * `payload`
  * `error` - boolean that identifies whether or not action represents error
  * `meta` - extra information that is not part of payload but might be useful when processing payload
* Enforce FSA by creating a utility method for creating actions that only allows `type`, `payload`, `error`, and `meta` arguments
* Proposed the use of `static` classes for action creation
  * Benefits are that the `static` class itself is self-documenting i.e. `SomeActionClass.someStaticActionMethod()` or `SomeActionClass.SOME_ACTION` in reducer
* Proposed convention for API request actions
  * `SomeAction.REQUEST_SOMETHING` and `SomeAction.REQUEST_SOMETHING_FINISHED`
* The basic workflow of an effect is
  * Make HTTP request and get response
  * If request has errored, return error
  * Else, return a `Model` instance with response data
  * Do _not_ modify API data for a given view
  * Pass data straight to reducer and let any `selectors` transform the data for views
* Effects are weaved into actions with the following workflow
  * `dispatch` an "I am fetching data / performing some async work" action
  * Call `effect`
  * When `effect` has resolved, `dispatch` an "I am finished fetching data / performsing async work" (`dispatch` generally contains data / error)

