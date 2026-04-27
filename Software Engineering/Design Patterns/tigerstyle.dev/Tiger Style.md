# [Tiger Style](https://tigerstyle.dev)
* Set explicit upper bounds on loops, queues, etc
  * Prevent infinite loops and uncontrolled resource usage
* Avoid implicit defaults: explicitly specify options when calling library functions instead of relying on default
  * Implicit defaults can change between library versions or across environments
  * Being explicit improves code clarity
* Avoid abbreviations - use full words in names unless the abbreviation is widely accepted and clear (ID, URL)
* Include units or qualifiers in names
  * Append units / qualifiers to variable names, placing them in descending order of significance (\`latency_ms_max\` instead of `max_latency_ms`)
* Minimize variable scope by declaring variables close to their usage and within the smallest necessary scope
