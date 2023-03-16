# [What Is Huffman Coding?](https://www.baseclass.io/huffman-coding/)

* Inevitably some characters will appear more often than others in the text to be compressed
* "do or do not"
  * If each character takes 8 bits to store, the entire phrase takes 96 bits
  * Build tree structure where most common characters are closest to root of tree while nodes further away are less common characters
  * So "o" would be closer to root than "t"
  * In this tree, store the path to the character instead of the character itself
  * So if character is in right tree from root, then left from next, then left from next again, can encode that as `1 (right) 0 (left) 0 (left)`
  * So one way to encode `do or do not` would be `100 00 01 00 101 01 100 00 01 110 00 111` which is 29 bits and no data loss
* To decode, follow each `0/1` until a character is reached
* To send encoded text, other side needs same Huffman tree
  * Can send tree along with compressed text
  * Could send the total number of times each character occurs along with other information to allow other side to build same exact Huffman tree to decode the text (this is what GZIP does)
