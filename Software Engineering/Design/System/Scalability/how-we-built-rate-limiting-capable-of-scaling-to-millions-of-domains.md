# [How we built rate limiting capable of scaling to millions of domains](https://blog.cloudflare.com/counting-things-a-lot-of-different-things/)

## Sliding windows

* Use information from previous counter in order to extrapolate an accurate approximation of the request rate
* If there is a limit of 50 requests per minute and there were 42 requests in previous minute and 18 requests in current minute (first 15 seconds), how to combine these pieces of information?
* Rate approximation is calculated like so `42 x ((60 - 15) / 60) + 18`
  * This rate approximation assumes constant rate of requests during previous sampling period
  * Smoothes traffic spike issue
  * Simple and easy (no average vs. burst traffic configuration)
  * From Cloudflare's analysis of 400 million requests, only about 0.003% were wrongly allowed or rate limited
  * 6% difference between real rate and approximate rate
  * There were 3 sources out of the 270,000 sources that allowed traffic above threshold (false negatives)
    * Actual rate was less than 15% above threshold rate
  * None of the sources were below threshold (false positives)
* To calculate rate the following needs to happen
  * Two numbers per counter (efficient memory)
  * Incrementing counter can be done via single `INCR` command
  * Rate is relatively simple - one `GET` and a pretty simple calculation
* Downsides
  * Still uses memcached query which can be susceptible to large memcached attacks
  * Will also slow down legitimate requests
* Cloudflare increments asynchronously to avoid slowing down requests
  * If request rate is violated, then all servers in point-of-presence will apply mitigation
  * This boolean is checked during request processing
  * Once a mitigation has started, when the mitigation has finished can be calculated
  * The mitigation finished time stamp can be saved on the server so a query doesn't even need to be called for subsequent requests
