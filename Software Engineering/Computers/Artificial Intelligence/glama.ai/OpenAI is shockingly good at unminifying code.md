# [OpenAI is shockingly good at unminifying code](https://glama.ai/blog/2024-08-29-reverse-engineering-minified-code-using-openai)

* Author comes across an interesting block of ASCII art, but couldn't figure out how it was implemented
* Identified source code that was minified, copied the code, and prompted ChatGPT to "explain the code"
* The response from ChatGPT gave an overview explaining the key components functions, etc
* Author asked for ChatGPT to implement equivalent code in TypeScript, and make it human-readable
* The resultant implementation was quite good and readable