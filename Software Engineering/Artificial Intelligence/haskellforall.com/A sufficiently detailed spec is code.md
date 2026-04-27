# [A sufficiently detailed spec is code](https://haskellforall.com/2026/03/a-sufficiently-detailed-spec-is-code)
* Author contends that there are two common misconceptions from AI enthusiasts that claim to generate code purely from specification documents
  * The first misconception is that specification documents are simpler than the corresponding implementation
  * The second misconceptions is that specification work must be more thoughtful than implementation work
* Author uses an example project (OpenAI's Symphony project)
  * Author contends that this project that is theoretically generated from a "specification" (i.e. a `SPEC.md` file) is more like pseudo-code in Markdown format
* Author contends that the generated service startup logic basically reads like code
  * And so, a specification document precise enough to reliably generate a working implementation must necessarily contort the document into code or something strongly resembling code (highly structured and formal English)
  * Effectively, can only transmute the implementation labor into something superficially different that still demands the same precision
* Specification work is supposed to be harder than actually implementing the feature / writing code
* Author contends that Symphony's specification document only superficially looks like a specification document
  * This specification document lacked coherence / clarity and large engineering organizations can no longer assume that specifications are the product of careful thought and deliberation
* Author contends that optimizing for delivery time implies authoring the code directly vs. writing an intermediate specification document
