# [Implementing a Trie data structure in Python](https://towardsdatascience.com/implementing-a-trie-data-structure-in-python-in-less-than-100-lines-of-code-a877ea23c1a1)

* Trie does not store a character in root node
  * If character does not exist, add first character as a child node, else skip adding it
  * Check child nodes of first character and see if second character is present in any child nodes of first character
* `add` method takes a root node and a word
  * Iterates through word, one character at a time
  * Checks if current node has a child node with current character
  * If no child node exists, create new node with current character and adds node to current node
  * If found or created, child node is now new current node and next character is ready to be processed
  * Can also add counter for number of times a given prefix is seen - this might be useful when displaying information about a given prefix
  * Once a word is completed, a node is marked as finished
* `find_prefix` method takes a root node and a prefix to search for
  * Iterating through the prefix one character at a time, it searches through the children of the current node, starting with the root node to find a match of the current character
  * If a match is found it reassigns the child node as the current node
  * If a match is not found, `False` is returned
  * If every character is correctly matched, return `True`
