# [`PUT` vs. `PATCH`](https://stackoverflow.com/a/34400076/5225575)

* `PUT` is considered idempotent
* `PUT` also assumes that
  * you're referring to an entity
  * and the entity is fully-constructed

Let's say you have this user

```json
## /users/1

{
    "username": "skwee357",
    "email": "skwee357@domain.com"
}
```

If you want to `PUT` an updated email address for this user, you would do something like

```json
## PUT /users/1

{
  "username": "skwee357",
  "email": "foo@bar.com"
}
```

while a `PATCH` would look something like

```json
## PATCH /users/1

{
  "email": "foo@bar.com"
}
```

* Because `PUT` requires the entire entity, making the same request over and over again will read to the same modifications on that entity (hence it's idempotency)
* This also means that `PUT` can lead to data loss if the entire entity is not sent

```json
## PUT /users/1

{
  "username": "skwee357"
}
```

Might result in a `null` value for `email` going forward because that's how the service interprets a missing `email` field.

* `PATCH` might not be idempotent when `PATCH`ing a list of users where the `PATCH` operation can add a user
