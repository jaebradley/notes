# [Postgres Window Functions](https://thoughtbot.com/blog/postgres-window-functions)

* Table of posts and comments
* Want to get each posts three most recent comments
* Query for all posts and comments, then filter in application
* Does not work at scale (thousands of posts and thousands of comments)
* All built-in aggregate functions can be used as window functions when combined with `OVER`

```psql
SELECT
  post_id,
  comment_id,
FROM (
  SELECT
    posts.id AS post_id,
    comments.id AS comment_id,
    dense_rank OVER (
      PARTITION BY post_id
      ORDER BY comments.created_at DESC
    ) AS comment_rank
  FROM posts
  LEFT OUTER JOIN posts.id ON comments.post_id
)
WHERE comment_rank < 4
```

* Inside `OVER` it's saying "rank comments by creation time and group comments by post id"
* If have to handle `nul`s then use a `case` statement around `rank` (`case when x is not null then rank over partition`)
  * https://stackoverflow.com/a/18194109/5225575
