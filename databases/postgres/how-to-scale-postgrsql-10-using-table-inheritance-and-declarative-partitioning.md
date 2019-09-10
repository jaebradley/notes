# [How to scale PostgreSQL 10 using table inheritance and declarative partitioning](https://medium.com/timescale/scaling-partitioning-data-postgresql-10-explained-cd48a712a9a1)

* Relational databases store tables as pages of data (8kb in PostgreSQL)
  * B-trees are built to index the data for faster lookups
  * If data is large enough that can't fit all pages of index into memory, then updating random part of tree 
