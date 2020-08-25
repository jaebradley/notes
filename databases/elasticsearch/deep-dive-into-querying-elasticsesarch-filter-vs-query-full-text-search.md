# [Deep Dive Into Querying Elasticsearch. Filter vs. Query. Full-text Search](https://towardsdatascience.com/deep-dive-into-querying-elasticsearch-filter-vs-query-full-text-search-b861b06bd4c0)

* The relevance score is a strictly positive float that indicates how well each document satisfies the searching critiera
  * This relevance score is relative to the highest score assigned, therefore, the higher the score, the better the relevance of a document
* Filter context is a yes/no option where each document either matches the query or does not (like SQL `WHERE`)
  * Filters are automatically cached and do not contribute to the relevancy score
  * Searches on exact values (range, keyword, etc)
* A term query or structured query, are queries mostly designed to return a set of documents that match a specific filter
  * Add a `constant_score` filter to a term query so that the query is not calculating a relevancy score, making the response faster
  * When specifying a list of values in a terms query like `"name": ["Frigg", "Odin", "Baldir"]`, it does not have to match all values in the field, only one

## Elasticsearch Analyzer

* There are zero or more character filters, one tokenizer, an zero or more token filters
* The character filter receives the raw text data and preprocesses the data before tokenization
  * For example, cleaning HTML text
* The tokenizer breaks the character-filtered text data into tokens
  * The `whitespace` tokenizer simply breaks text by whitespaces
  * The standard (default) tokenizer breaks text by whitespace after removing punctuation
  * Tokenizer keeps track of tokens order, notes the start and end of each word, and defines the type of token
* The token filter applies transformations on the tokens
  * Like making the tokens lowercase, or removing dupiicates
* Full-text query searching will use the same analyzer that was used while indexing the data
* When specifying a `match` query, it is by default an `OR` operation - at least one should match, but more matches will lead to a higher score
* The `fuzziness` parameter (which is optional) allows you to omit typos - it utilizes Levenshtein distance
* The `match_phrase` query is the same as the `match` query but the sequence order and proximity matter
  * The `match_phrase` query has a `slop` parameter which is responsible for skipping terms. So a `slop` of `1` means that one word out of the phrase may be omitted
