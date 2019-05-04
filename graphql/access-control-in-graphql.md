# [Access Control in GraphQL](https://blog.apollographql.com/auth-in-graphql-part-2-c6441bcc4302)

* Authentication is checking identity of user making a request
* Authorization is set of rules that are applied to determine what a user can do
* Non-primitive types has fields which define the relationship between one type and another type
* In graph terms, the types in schema are nodes and the fields in schema are edges
* Graph has no root, so schema needs to have root query type, which is entry point into graph
* Permissions on edges
  * Let's say that only certain users can see a list of todos
  * If there's a `getListById` query, you'd check to see if the user should be able to see the list
  * Downside is that there are potentially a bunch of places in the schema where a list of todos can be accessed (i.e "multiple edges that lead to a particular node")
  * Should just put permissions on node

