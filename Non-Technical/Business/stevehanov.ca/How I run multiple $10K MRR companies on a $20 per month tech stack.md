# [How I run multiple $10K MRR companies on a $20/month tech stack](https://stevehanov.ca/blog/how-i-run-multiple-10k-mrr-companies-on-a-20month-tech)
* Author uses Linode or DigitalOcean and rents a single Virtual Private Server and spends ~$5-$10 per month
  * Author uses a machine that uses ~1 GB of RAM
* Author builds backend systems using Go
* Go is beneficial due to typing, and ability to compile application to binary that can be executed on the rented server
* Author runs a local LLM using a GPU
* Author uses laconic, a tool that intentionally only runs a 8K context window
  * It manages context like an operating system’s virtual memory manager and pages out parts of the context that are no longer relevant
* Microsoft/GitHub charges by “request” and not by token
  * A “request” is each chat request/response
  * So even if the agent spends X minutes executing ABC operation, the author still gets charged for a singular request (a few cents)
  * Author suggests detailed prompts, with strict success criteria, and tell the agent to “keep going until all errors are fixed”
* Author suggests using a local SQLite file as it is orders of magnitude faster than a TCP network hop to a remote Postgres serverstack)
  * Most people think SQLite locks the whole database on every write but need to turn on Write-Ahead Logging
