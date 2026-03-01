# [(Almost) Every infrastructure decision I endorse or regret after 4 years running infrastructure at a startup](https://cep.dev/posts/every-infrastructure-decision-i-endorse-or-regret-after-4-years-running-infrastructure-at-a-startup/)
* With rare exceptions, never regretted prioritizing taking some time to write automation / documentation to ensure team is efficient over external demands
* A shared database has strong data integrity via foreign key relationships
  * Since a shared database is used by everyone, it becomes cared for by no one
  * Everything owned by no one is owned by infrastructure, eventually
  * Crud accumulates in the database and it's unclear what can be deleted
  * Where there are performance issues, infrastructure (without deep product knowledge) has to debug database queries
  * Application teams push code changes that are not optimized from a database perspective
    * These changes may alert the infrastructure team, since they now own the database
    * If there are application-specific databases, then the application team is the first responder
* Google Workspaces was not flexible enough, even when creating groups of employees to assign permissions
  * Eventually switched to Okta
  * Lean into an identity solution early on and only accept SaaS vendors that integrate with that identity solution
* Linear > Jira, PagerDuty is great, use Slack and Notion
* Datadog is a good product, but expensive
  * Pricing model is based on the number of instances you have
  * So if at any given time there are at most 10 instances, but 20 instances have been spun up/down in some time frame, then Datadog charges for the 20 instances
* Buy your own IPs
  * If you work with external partners, you'll frequently need to publish an allowlist of your IPs
  * Buying your own IP block is a great way to avoid this by giving external partners a larger CIDR block to allowlist
