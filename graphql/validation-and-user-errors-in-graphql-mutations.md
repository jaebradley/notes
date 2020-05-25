# [Validation And User Errors In GraphQL Mutations](https://medium.com/@tarkus/validation-and-user-errors-in-graphql-mutations-39ca79cd00bf)

* Often times a GraphQL error message looks like

```json
{
  "errors": [
    {
      "message": "Field X of type Y is required but not provided"
    }
  ]
}
```

* For inputs, it would be great is error messages contained name of input argument that didn't pass validation and why it failed
* Woudl also be helpful if it supported multiple error messages per input field

```json
{
  "errors": [
    {
      "message": "Request is invalid",
      "path": ["CreateUser"],
      "state": {
        "": ["Failed to create a new user"],
        "email": ["A user with this email already exists"]
      }
    }
  ]
}
```

* The `state` field is an object with keys representing the name of the input parameters that didn't pass server-side validation and an array of error messages for each of the keys
* There's a special case `""` field that may contain one or more errors that are not related to any particular input field
* In client app, use this info to highlight form fields that caused these errors and ask user to fix errors and submit form again
* When resolving a particular mutation, would use validation logic and build this `errors` object