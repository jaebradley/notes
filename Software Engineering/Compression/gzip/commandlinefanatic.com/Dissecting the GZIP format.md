# [Dissecting the GZIP format](https://commandlinefanatic.com/cgi-bin/showarticle.cgi?article=art001)

* GZIP implements the DEFLATE algorithm
* DEFLATE is a combination of LZ77, Huffman codes, and run-length-encoding
* LZ77 compression uses concept of “backpointers”
* Backpointer form is <distance, length>
* <25, 5> means when uncompressing the document, search backward 25 characters and reproduce the 5 characters found there
* Decoder/decompressor needs to distinguish between input bytes for backpointers and the actual literal bytes
* Naive approach is to introduce an escape code (0x255) to distinguishbackpointer bytes from literal bytes
* GZIP uses variable-length codes instead
* The first 255 characters in this variable-length “alphabet” are the literal codes - the 8-bit bytes that were read from the input stream
* The 256th character is a stop code that tells the decoder when to stop decoding
* The 257th-285th codes indicate the length of the matched range
* In a fixed-length world, this 285-symbol alphabet would require 9 bits per symbol
* However, would only use a little more than half of the available 512 bytes that 9 bits can encode
* Imagine 4 character alphabet with four variable-length codes: A -> 1, B -> 0, C -> 10, D -> 11
* Can’t distinguish if “10” is “AB” or “C”
* Solution is to assign prefix codes. Prefix codes delineate the range of codes, but can’t be used as a code itself
* If “A” is assigned “1”, no other code can start with “1”
* If “B” is assigned code “01”, no other code can start with “01”
* So previous “alphabet” is A -> 1, B -> 01, C -> 001, D -> 000
* Only one 1-bit code, two 2-bit codes, two 3-bit codes, four 4-bit codes, etc
* Country calling codes are prefix codes
* Prefix codes referred to as Huffman codes
* Usually expressed as a tree, where each leaf is a symbol and each branch is a bit value
* Each leaf node represents a “character” in the “alphabet”
* When reading an input stream, process each bit. And follow the Huffman tree for each bit value (0, 1, 0, 1, 1, 0, etc) until a leaf node in the Huffman tree is identified. Use the “prefix code” for this leaf node, reset the node to the head of the tree to process the next set of bits

## Decoding Huffman codes example

* If the input consists of mostly Cs (001) and Ds (000), the encoded output will be longer than if the input consisted mostly of As (1)
* Intelligent assignment of Huffman codes to symbols (i.e. assigning high-frequency symbols to short codes and low-frequency symbols to longer codes) can increase the compression rate
* GZIP mandates that the input is first LZ77 compressed, and then this compressed output is Huffman-encoded
* Different inputs have different statistical distributions of symbols, so GZIP format requires that the LZ77 compressed output specify a Huffman code table
* Deflate format places additional constraints on the construction of the Huffman code table, so that the table can be completely specified by outputting the lengths of each code
* 6 four-bit codes, 8 five-bit codes, and 12 six-bit codes
* Example way of ensuring that each symbol can be decoded unambiguously: all the four-bit codes start with 0, all the five-bit codes start with 10 and all the six-bit codes start with 11
* The proper Huffman code assignment procedure doesn’t prefix-code everything, but instead it
* Finds the last code in the previous bit length
* Left shifts it by 1 (adds a zero at the end)
* Starts the next bit length from that value

## Call the inflate routine

* Bit flips can make a document decompress correctly, but inaccurately
* Cyclic Redundancy Check is at the end of every document
* Decompressor can compre CRC and check if document has been correctly inflated
* CRC is a more reliable checksum
* Checksum is summing all the input bytes modulo some agreed upon base, and then transmit the resulting sum
* Checksums are not optimal when detecting bit errors because if two bits are flipped they can flip in such a way to cancel each other out and the checksums won’t detect an error
* CRCs are less susceptible to this error
