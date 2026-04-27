# [A Principled Approach to Querying Data](https://www.claudiu-ivan.com/writing/search-dsl)

## Error Handling
* The `Either` type (functional programming) provides this mechanism via a value that can be a `Right` (success) or a `Left` (failure) 
* If a parsing step succeeds, it’ll return a `Right` containing the parsed value
* If the parsing step fails, it’ll return a `Left` containing an error object

## Parsing with precision
* A parser combinator is a higher-order function
* It takes multiple parsers and returns a new parser
* Examples of some parsers:
  * Literal string match
  * First matching parser
  * Applies multiple parsers sequentially, succeeding if only all parsers match
  * Transforms successful parse using a mapping function

## Structuring the query
* The parser is responsible for transforming the input string into an Abstract Syntax Tree
* Leaf filter nodes represent individual filters, that are combined into non-leaf filter nodes via and/or statements
* In this case, the leaf filter nodes have a type of value that they are filtering on, like “status”, “author”, “label”, etc
* The top-level “search query parser” is created by combining the combinators
* Exaample search query parser = “apply the first matching parser amongst a status parser, author parser, label parser,etc”
* Example status parser = “string where the prefix is the literal string ‘is:’ and the remaining string is the literal string ‘open’ or ‘closed’”

## Evaluating the query
* Can build “predicates” for each filter type (author, status, etc)
* Example matching status predicate = “is the specified status value valid, and does it match the current issue’s status value?”
* Can build all these specific filter status predicates into a top-level predicate creator that recursively iterates over the query’s abstract syntax tree
* Then can attempt to execute the created predicate function (based on the input filters) on each of the issues and see which ones match

```js
const createPredicate = (node: FilterNode): IssuePredicate => {
  switch (node.type) {
    case "status":
      return isValidIssueStatus(node.value)
        ? matchStatus(node.value)
        : matchNone();
    // ... other cases
    case "and":
      return and(...(node.value as FilterNode[]).map(createPredicate));
    // ...
  }
};

const and =
  (...preds: IssuePredicate[]): IssuePredicate =>
  (issue) =>
    preds.every((p) => p(issue));

const executeQuery = (query: string, issues: Issue[]): Issue[] => {
  const parsedQuery = searchQueryParser(query);

  if (parsedQuery.isLeft()) {
    console.error(`Parse Error: ${parsedQuery.value.message}`);
    return []; // Return empty array on parse failure
  }

  const ast = parsedQuery.value[0];
  const predicate = createPredicate(ast);
  return issues.filter(predicate);
};
```
