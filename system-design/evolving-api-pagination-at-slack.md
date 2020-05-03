# [Evolving API Pagination At Slack](https://slack.engineering/evolving-api-pagination-at-slack-1c1f644f8e12)

## Offset-Based Pagination

* Clients provide a parameter indicating the number of results they want per page (usually something like a `count` parameter) and a parameter indicating the page number they want to return results for (usually something like a `page` parameter)
* In an SQL database, implementing this is fairly straight-forward
  * Count all the results to determine the total number of pages
  * Then use `page` / `count` to query for the items for the requested page
    * So if `page` is `3` and `count` is `20`, we can tell database to skip the first `40` items and return the next `20` (`LIMIT 20 OFFSET 40`)
* So server response would look something like

```json
{
  "pageInfo": {
    "total": 300,
    "page": 3,
    "pages": 100
  }
}
```

* Gives the usuer the ability to see total number of pages and progress through that total
* Gives user the ability to jump to a specific page
* `LIMIT / OFFSET` doesn't scale well for large datasets as when `OFFSET` increases, the database still has to read `OFFSET + COUNT` rows from disk, before discarding the `OFFSET` rows and only returning the `COUNT` rows
* If items are written to the database at a high frequency, the page window maybe become stale / unreliable
  * Paginating through the last X messages in a channel - if 10 messages have been added to channel in interim, fetching the second page will look identical as what we saw on the "old" first page

## Cursor-Based Pagination

* Works by using a pointer to a specific item in a dataset
* Server returns results from the given pointer
* Cursor must be unique (generally an auto-incrementing `id` on a SQL table, for example)
* No concept of total number of pages - which also means that client can't jump to a specific page
* Implementation-wise, the client would pass a `cursor` and `count` parameters where the `cursor` value would filter results in the `WHERE` statement (something like `WHERE id >= cursor`) and the `count` would be used to determine the size of the `LIMIT` on the query (returning `count + 1` so we can identify the next cursor as part of the response)
* The next cursor value can be used to get the next set of results, effectively paginating through the list of results - when the next cursor is empty, this means that there are no more results to fetch
* This approach scales well for large tables as the `WHERE` can leverage index on the column and avoid the `OFFSET` + `COUNT` problem
* If items are written to the dataset at a high frequency, the overall position of the cursor in the set might change, but the pagination window adjusts accordingly