# [The Prompt Engineering Playbook for Programmers](https://addyo.substack.com/p/the-prompt-engineering-playbook-for)

![image](https://github.com/user-attachments/assets/876f3ad2-90dc-4486-b242-57d9328a0b0c)

* Prompt will often times have setup context like "I have a Node.js function using XYZ technology throwing ABC error with this error code and error message"
* "Why isn't my code working?" vs. "This JavaScript function is returning undefined instead of the expected result ABC. Given the code, can you help identify why and how to fix it?"
  * Prompt formula: `It’s expected to do [expected behavior] but instead it’s doing [current behavior] when given [example input]. Where is the bug?`
* Few-shot prompting where the prompt provides examples of what the expected behavior should be given a ste of inputs
* "Act as a senior React developer and review my code for potential bugs", "You are a JavaScript performance expert; optimize the following function"
* Maintain well-structured code and comments with consistent formatting as the AI will continue to follow these patterns

## Prompt debugging patterns
* Use a step-by-step approach: "walk through this function line by line and track the value of ABC at each step. It does not seem to be the correct value - where does the code incorrectly calculate ABC?"
* Focus the AI into specific areas of the code: "explain what the XYZ method is doing here and if it might be incorrectly excluding more items than it should"
* “Why isn’t my mapUsersById function working?” vs. “I have a JavaScript function mapUsersById that should convert an array of user objects into a map (object) keyed by user ID. However, it throws an error when I run it. For example, when I pass [ {id: 1, name: "Alice"} ], I get TypeError: Cannot read property 'id' of undefined. Here is the function code: ABC. It should return { "1": {id: 1, name: "Alice"} }. What is the bug and how can I fix it?”
* Have the AI generate test cases: "can you provide a couple of test cases that might break this function"
* "Act as a code reviewer - review and point out any mistakes or bad practices that could be causing issues"

## Prompt patterns for refactoring and optimization
* Ask for explanations when refactoring ("suggest refactored code with explanations for your changes")
  * This acts as a safeguard if anything about the explanation does not make sense
* Role-play as an expert or experienced/senior developer to apply best practices
* Example: "Refactor the above getCombinedData function to eliminate duplicate code and improve performance. Specifically: (1) Avoid repeating the fetch logic for users and orders – maybe use a helper or fetch them together. (2) Fetch both lists in parallel if possible. (3) Keep the error handling for each fetch (we want to know which call failed). (4) Improve the combination of data, possibly by using a more efficient structure for lookup instead of a nested loop. Provide the refactored code with comments explaining the changes.”
* Refactor in steps - for example, refactor for readability first, then optimizations
* Ask for alternative approaches ("please use a functional programming style")
