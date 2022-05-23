# [How gzip uses Huffman coding](https://jvns.ca/blog/2015/02/22/how-gzip-uses-huffman-coding/)

* Text to compress:

> a tapping, as of someone gently 
r{apping},{ rapping}
at my chamber door

* gzip encodes this as

> a tapping, as of someone gently
r{back 30 characters, copy 6},
{back 9 characters, copy 8} at my chamber door

* Once gzip has gotten rid of repetitions, the next step is to compress individual characters that appear more frequently than others

## How gzip uses Huffman coding to represent individual characters

* gzip compresses bytes, so need to be able to represent some bytes using less than 8 bits
* Huffman coding is where characters like `a`, `b`, `c` etc are represented by bits (`00`, `010`, `011`, etc)
* These codes are designed where none of the codes are the prefix of another code so `01100010` -> `011` `00` `010` -> `c a b` and there isn't any ambiguity because there's no other way to "read" the bits / map the bits to an alternative representation
* All the "codes" should be less than 8 bits so that we can theoretically compress anything with characters
* Huffman tables are usually represented as trees, where the leaves of the tree represent the character and the path to a leaf (note these are binary trees too, since each branching is whether or not to append a `0` or `1` to the existing path) represents the "code" for the character
  * So the `a` character is represented by the code `00`, and within the tree, would be the leaf node that follows the `0` branch from the root node, and then the `0` branch from the following node
