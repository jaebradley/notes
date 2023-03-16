[# Tagging](https://git-scm.com/book/en/v2/Git-Basics-Tagging)

__TL;DR__ People often use tags to mark release points

## Commands

* `git tag [-l]` (list tags)
* `git tag -l "v1.8.5*"` (list tags of the `1.8.5` series)
* `git show` (display tag data along with the commit that the tag points to)
* `git push [origin] <tagName>` (push a tag to a shared server)
* `git checkout <tagName>`

## Annotated Tags

* Have the following details
  * Checksummed
  * Contain Tagger Information (name, email)
  * Date of Tags
  * Message
