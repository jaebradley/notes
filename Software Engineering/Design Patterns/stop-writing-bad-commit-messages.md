# [Stop Writing Bad Commit Messages](https://medium.com/better-programming/stop-writing-bad-commit-messages-8df79517177d)

* A good commit message is structured into three parts (subject, body, closing line)
* Subject should be written in imperative tense ("add X feature")
  * Begin with capital letter
  * No period
  * 50 chars or less
  * Completes the sentence "This commit will..."
    * "This commit will fix bug" is not a particularly descriptive sentence so "fix bug" should probably not be the subject of a commit message
  * Body
    * Some commits (like fixing a typo) may not require a body as the subject should be informative enough
    * Explains why changes are necessary, why it's implemented in a particular way
    * Cover high-level details that may not be obvious from just reading the code
  * Closing line should be used to provide useful metadata about change like `JIRA` ticket number, GitHub issue number, co-authors, etc.
