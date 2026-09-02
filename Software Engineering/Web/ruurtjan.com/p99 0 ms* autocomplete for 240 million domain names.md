# [p99 0 ms* autocomplete for 240 million domain names](https://ruurtjan.com/articles/p99-0ms-autocomplete-for-240-million-domain-names)
* Author owns a website that utilizes autocomplete as the main user interface
* This autocomplete specifically is for domains names
* The autocomplete is implemented by prefetching domain name suggestions on keydown for the typed character
  * Domain name suggestions are returned for any next character in a mapping of `<next character>: set<domain names>` like `"-": ["wi-fi.ru", "wi-fi.org", "wi-fi.click", "wi-tribe.ph", "wi-cat.ru", "wi-fi.link", "wi-power.com", "wi-fi.com"]`
*  These domain name suggestions are rendered on key up
*  So the operation time budget is keydown + any time gap + next keydown

## Bandwidth
* There are 38 valid domain name characters (`a-z`, `0-9`, `-`)
* Autocomplete only returns `8` suggested domains for each character
* So the maximum number of domains in a response is `8` for the current prefix, and then `8` for each of the `38` valid domain name characters that populate the mapping of `<next character>: set<domain names>`
* This is a max of `~5 KB` per request and then `~2.5 KB` after compression

## Optimization
* Trie stores the top `8` most popular suggestions for each prefix in-memory
* For domains that aren't stored in-memory, there are memory-mapped fixed-size blocks that are stored on disk (SSD)
  * These memory-mapped blocks have an in-memory directory with relatively smaller resource footprint
  * This directory is binary-searched to find a matching disk block
  * Each disk block is linearly scanned to find a matching name
  * 240 million domain names takes about `2.5 GB` of disk space
  * Relies on OS caching to cache recently accessed disk blocks

## System
* Cloudflare is utilized as a CDN
* The underlying service uses `nginx` as a proxy
* p99 for just the `nginx` + underlying server was `15ms`
  * While this number could be improved, network latency accounts for most of the user's perceived latency
* Service runs on a single server in Europe, so uncached responses will far exceed any p99 budget
  * Multiple services in multiple regions with geo-loaded traffic would theoretically get the same low p99 latency everywhere
