# [Common Go Pitfalls](https://medium.com/better-programming/common-go-pitfalls-a92197cd96d2)

## HTTP Requests

* Default HTTP client doesn't actually have a timeout
  * This means that code that uses default client hangs indefinitely depending on server or until application restarts
  * Define timeout for `http.Client`
  * Attach context to a request
    * Allows ability to cancel ongoing requests
    * Tune timeout for specific requests (allows the ability to adjust timeout for requests that you know are going to be longer-lived than most)

```go
ctx, cancel := context.WithTimeout(context.Background(), time.Minute)
req = req.WithContext(ctx)

res, err := c.Do(req)
```

* If allotted time is exceeded, result will be `DeadlineExceeded` error

## Database Connections

* `sql.DB` object is a concurrency-safe pool of connections (instead of single database connection)
* Must remember to return connections to pool, or else exhaust total number of connections for application
* Do not forget to close `*sql.Rows` object (when querying)
  * If not, will lead to max connections limit and cause deadlock / high latency

```go
rows, err := db.QueryContext(ctx, "SELECT foo FROM bars")
defer rows.Close()
```
