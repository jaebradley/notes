# 📔 Notes from Things I've Read

Mostly notes on technical topics (kind've like my own [`DevDocs`](https://devdocs.io/)) though there are some articles on productivity, life lessons, etc.

![alt-image](https://i.giphy.com/media/VgY4dDdN1W3NS/giphy.webp)

## Content Organization Principles
* Prefer semantic grouping over lexical grouping
  * In other words, group content together because they share a conceptual domain and **not** just because they share common words or prefixes
* Prefer verbose and descriptive folder and file names
* Prefer case-sensitive file names
  * Yes, there are some downsides when it comes to cross-platform compatibility, but as this content is mostly for my own reference, this is my preference
* Use space-separated file and folder names
  * Prefer `Amazon Web Services` over `AmazonWebServices` or `amazon-web-services`

## File Content
* For content that is online, like a web page or a video, strongly prefer to name the file to match the title of the content
  * This content is stored in a directory with the same name as the effective top-level domain of the content's URL 
  * At the top of the Markdown file, include the title of the content as a level 1 heading, and include the URL to the content in a link immediately following the title like the following:
```markdown
// This content is in file "Title of the Content.md"
// The "Title of the Content.md" file is in directory "example.com"
# [Title of the Content](https://example.com/url/of/content)
```
