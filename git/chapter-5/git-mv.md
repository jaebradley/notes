# `git mv`

* These are equivalent

```bash
mv stuff newstuff
git rm stuff
git add newstuff
```

and

```bash
git mv stuff newstuff
```

* `git mv` will also preserve history
  * While `git log newstuff` might make it seem like only the history since the move exists, by default `git` will only display the log for the current filename
  * In order to tell `git` to "follow" all history for the given content, use the `--follow` flag like `git log newstuff --follow`
