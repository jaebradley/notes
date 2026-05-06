# [Redis array: short story of a long development process](https://news.ycombinator.com/item?id=48009172)
## Commenter’s approach
* Start with a high-level design doc, which is co-authored by AI
* Ask another AI, same model without context, or another model, to critique the design doc, spot bugs, gaps, and omissions
* Summarize findings and copy/paste that output to the co-authoring AI and ask the co-authoring AI opinions on these findings
* Continue this round-robin until no model suggests anything that seems weighty
* Then, ask AI to make a plan, and then round-robin through these AIs until a solid plan is formed
* Commenter says that this is basically how Google built code for decades, just with humans of different levels of trust instead of AI