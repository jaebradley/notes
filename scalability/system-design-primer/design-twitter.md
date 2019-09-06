# [Design Twitter](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/twitter)

## Use Cases / Requirements

* User posts a tweet
  * Fan out service to push tweet out to followers
  * Fan out service should be fast (in case of millions of followers, it should only take a couple of minutes)
* Users are able to view the tweet timeline for a given user
* Users are able to view the home timeline (activity from people the user is following)
* Both timeline operations are fast
* User's Twitter experience is often more read-heavy than write-heavy
* Users are able to search keywords
* Traffic is not evenly distributed
  * Top Twitter users will get more traffic

## User posts a tweet

* Client posts tweet to web server (perhaps running behind a reverse proxy)
* Store user's tweets in a SQL database
* Message Fan Out Service to publish tweet to followers
* Fan Out Service talks to User Graph Service to find followers
  * Followers are stored in a memcache
  * Home timeline of user's followers is stored in memcache
    * O(n) operation - so if user has n followers, there will be n writes
    * If Redis, can use list where each element in list has the tweet id, user id, and metadata for tweet
* Store tweet in Search Service
  * Search Service indexes tweet, probably using an inverted index to map keywords to documents

## User views their home timeline

* Client makes request to web server
* Web server messages Timeline Service
* Timeline Service gets list of tweets from memcache
  * Queries Tweet Data Service with tweet ids to get additional metadata to serve client
  * Queries User Data Service with user ids to get additional metadata to serve client

## User views their timeline

* Read tweets from SQL database with each user's tweets

## Search Service

* Search Service parses / tokenizes input query 
  * Removes markup, handles capitalization
  * Tokenizes text
* Queries some type of searching-based data-store like Lucene

## Optimizations

* Fanout Service
  * Fanout process for users with millions of followers could take long time
  * Re-order tweets at serve time
    * Don't fan out tweets for highly-followed users but merge them with user's home timeline when serving
* Home Timeline
  * Only keep several hundred tweets for each home timeline in memcache
  * Only keep active users home timelines
    * Rebuild a user's home timeline by querying user graph service, and then get tweets from SQL
* Tweet Data Service stores some TTL'd set of tweets (i.e. tweets for only the past month)
* User Data Service only stores active users
* Tweet SQL DB might not be able to scale even with read-replicas

