# [Avoiding Duplicate Objects in Django Querysets](https://johnnymetz.com/posts/avoiding-duplicate-objects-in-django-querysets/)
* When filtering a queryset by traversing a relationship, Django performs an SQL JOIN
* If a parent object has multiple related objects that match your filter, the parent object appears multiple times in the result set
* `Author` can have multiple `Books`
* `Author.objects.filter(books__title__startswith="Book")` could return `[Author A, Author B, Author A]` 
* `.distinct()` can be expensive, especially for large fields like `JSONField` and `TextField`
  * The database has to compare these fields across all rows, which can be expensive
  * Postgres supports `DISTINCT ON` statements which is more efficient than comparing all fields
    * Limitation is that the `SELECT DISTINCT ON` expression match the `ORDER BY` expression
* `Exists` subquery is structured something like
```python
from django.db.models import Exists, OuterRef

Author.objects.filter(
    Exists(
        Book.objects.filter(
            author=OuterRef("id"),
            title__startswith="Book",
        )
    )
).order_by("name")
```
* `Exists` uses the `SQL EXISTS` statement
* Will perform better than a subquery since the database is able to stop evaluation of the subquery when a first matching row is found
* No ordering restrictions and works with all databases
