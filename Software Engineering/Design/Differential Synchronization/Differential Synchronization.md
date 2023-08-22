# [Differential Synchronization](https://neil.fraser.name/writing/sync/)

## Differential Synchronization Overview

* Client Text and Server Text documents are located on the same computer with no network
* Client Text, Server Text, and Common Shadow documents all start as being equal
* Client Text and Server Text are editable

### Steps

* Client Text is diffed against the Common Shadow
* Diff returns a list of edits that have been performed on Client Text
* Client Text is copied over to Common Shadow
  * A snapshot of the Client Text when diffing occurs is used
* Edits are applied to Server Text on a best-effort basis
* Server Text is updated with the result of the patch
* The previous two steps must be atomic (but not blocking)
  * In other words, they may be repeated until Server Text stays still long enough
* Previous steps are repeated symmetrically in the other direction
  * Common Shadow is now the same as the Client Text
  * Diff is now modifications made to Server Text that are not part of the patched edits from the Client Text diff

## Example

* All documents start with "Macs had the original point and click UI."
* Client Text is edited to say "Macintoshes had the original point and click interface."
  * A diff would return two edits
  * Adding "intoshe" and replacing "UI" with "interface"
* Common Shadow is updated to say "Macintoshes had the original point and click interface."
* Server Text is concurrently edited to say "Smith & Wesson had the original point and click UI."
* The two edits above would be patched onto Server Text
  * First edit would fail as no place to add "intoshe"
  * Second edit would succeed since "UI" can be replaced with "interface"
* Server Text now says "Smith & Wesson had the original point and click interface."
* Reverse process, so generate a diff comparing Server Text to Common Shadow
  * Edit is replacing "Macintoshes" with "Smith & Wesson"
* Patch is applied to Client Text so now it says "Smith & Wesson had the original point and click interface."
