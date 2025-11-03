# [Don’t Forget These Tags to Make HTML Work Like You Expect](https://blog.jim-nielsen.com/2025/dont-forget-these-html-tags/)
* Without `<!doctype html>` browsers may switch to quirks mode, emulating legacy, pre-standards behavior
  * Impacts how calculations work around layout, sizing, alignment
* `<html lang="en">` declares the document's language
  * Browsers, search engines, assistive technologies leverage this for pronunciation and screen readers, indexing, applying locale-specific tooling (like spell checkers)
* `meta utf-8` can be returned by the server via a header `charset=utf-8` but best set in the HTML document specifically
* `<meta name="viewport" content="width=device-width,initial-scale=1.0">`  is useful for different device types (like mobile)
