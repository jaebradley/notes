# [Difference between URI, URL and URN](https://mohitgoyal.co/2017/06/09/difference-between-uri-url-and-urn/)

* A URI is a compact sequence of characters that identifies an abstract or physical resource
* A URI can be further classified as a locator, a name, or both
* A URL is a subset of URIs that both identifies a resource and provides a means of location the resource by describing its primary access mechanism
* One can classify URIs as locators or as names or as both
  * A URN functions like a person's name
  * A URL functions like a person's street address
  * URN defines an item's identity while the URL provides a method for finding it
* The part that makes a URI a URL is the inclusion of the "access mechanism" or "network location" like `http://` or `ftp://`
* The URN is the "globally unique" part of the identification
* `http://files.metavrs.in`
  * `files.metavrs.in` is the `URN`
  * `http://files.metavrs.in` is both the URL and the URI
* `urn:oasis:names:specification:docbook:dtd:xml:4.1.2` is a URI and not a URL because it does not refer to a network location at all
