# [Design a Web Crawler](https://github.com/donnemartin/system-design-primer/tree/master/solutions/system_design/web_crawler)

## Initial Guidelines

* Crawl a list of URLs
  * Generate reverse index of words to pages
  * Generate titles and snippets for pages
* User inputs search term and sees list of relevant pages with titles and snippets from crawler
* Search service is highly available

## Initial Assumptions

* Traffic is not uniform (some searches are more popular than others)
* Search results should be fast
* Web crawler should not get stuck in infinite loop (link graph contains cycle - URL refers to URL that refers to original URL)
* 1 billion links to crawl
  * Links need to be crawled regularly to avoid staleness
  * Every link is crawled once per week
  * Average page size is 500kb
  * 2 PB of stored page content every month

## Crawler

* Initial list of links to crawl that's based on popular sites
* Generate page signatures based on a page's content (perhaps using a hash)
* Store crawled links in a NoSQL database
* Links to crawl is ranked based on popularity
* Processing loop looks like
  * Take highest ranked link to crawl
  * If link has already been crawled remove / reduce priority (avoid cycle)
  * If link has not already been crawled add job to reverse indexing service to generate inverted index
    * Add job to document service to generate title and snippet
  * Generate page signature for link
  * Remove link from links to crawl
  * Insert link and page signature into crawled links DB
* Remove duplicate URLs by using `MapReduce` to output entries that only have a frequency of 1
* Crawled results should also have a `timestamp` value that indicates the last time a page was crawled
  * More popular sites should be refreshed in shorter intervals

## Search Service

* Client sends request to web server (reverse proxy)
* Reverse proxy forwards request to Search API server
* Search API
  * Parses query
    * Removes markup
    * Breaks up text into terms / tokens
    * Fixes typos
    * Normalizes capitalization
  * Uses reverse index service to find documents that match query
    * Service returns most relevant documents
  * Document service is used to return titles and snippets

## Optimizations

* Popular searches can be stored in memory (Redis) to reduce load
* DNS lookup can be a bottleneck so Crawler Service can keep own DNS lookup
  * [DNS resolution can entail multiple requests and round-trips across internet taking seconds](https://nlp.stanford.edu/IR-book/html/htmledition/dns-resolution-1.html)
  * Web crawlers set up their own DNS resolver implementation such that threads will resume when DNS resolution occurs

