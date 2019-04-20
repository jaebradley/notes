# [What in REST, How in GraphQL](https://medium.com/open-graphql/graphql-2-d18d64b06da5)

## When to use REST

* Server-to-server
  * When primary communication is supposed to be from a server to another server vs. server to mobile / web platform (where network bandwidth can be severely limited)
    * Article uses example of news outlet exposing it's data to other news partners
* Have to pick one application approach for single server given that REST has been around for a longer period of time and more engineers / engineering resources are knowledgeable in REST

## REST to GraphQL migration

* Consider converting to GraphQL if
  * Performance issues with REST endpoints due to data fetching (overfetching or underfetching but with big payloads)
  * Client applications have to do a lot of data massaging or iteration (i.e. manipulating a bunch of data on the client, especially JavaScript might block the main thread)
* Couple ways to transition from REST to GraphQL
  * Set up a separate GraphQL service that wraps existing REST service
    * REST service still exists so GraphQL service is just a proxy for REST service
    * This way, existing clients that use REST service don't need to be migrated over just yet but new client applications can use the new GraphQL interface
    * Clients can then be migrated over incrementally
  * Build new functionality via GraphQL server
    * Clients will need to upgrade their logic to use GraphQL functionality when necessary
    * Could lead to bloated client-side logic / duplicate request handling
    * Bypass initial cost of transitioning existing REST APIs to GraphQL
