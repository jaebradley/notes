# [Why ORM Shouldn't Be Your Best Bet](https://medium.com/ameykpatil/why-orm-shouldnt-be-your-best-bet-fffb66314b1b)

* Problems with ORM
  * Makes it harder to optimize code
  * Since ORM naturally abstracts SQL, it's a little harder to see what's going on under the covers
  * Sometimes they have a steep learning curve
* ORM strategies
  * Use ORM and accept performance penalties
  * Remove ORM which is costly
  * Continue to use ORM but complex queries are written in raw SQL
* ORM has generally (significantly) slower benchmarks than raw SQL
* Perks of ORM
  * Schema checking
  * Built-ins (like helper methods and certain abstractions)
