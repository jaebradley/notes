# [The Two Abstractions of System Design: Hide or Reduce](https://muratbuffalo.blogspot.com/2026/05/the-two-abstractions-of-system-design.html)
* Modularity abstraction is "traditional" abstraction like APIs, layered design, encapsulation, hiding internals
* Modeling abstraction is for building models that represent the minimal and most elegant description that preserves the most relevant properties
* Modularity abstraction is about interfaces that hide internals
* In contrast, author argues that modeling abstraction is about reducing a system to its minimal behavior skeleton for the property you care about
* Modularity abstraction aspires to hide interleaving behavior and present operations as if they were atomic
* Goal is to make the module easy-to-use - in doing so, it forgoes exposing concurrency or efficiency opportunities
* In contrast, modeling abstraction is about identifying what should leak, proving that invariants hold
* Distributed system examples of modeling abstraction
  * Lamport logical clocks - throw away wall-clock time
  * Log is the database - throw away materialized state as the source of truth and keep only the ordered, append-only sequence of events
  * Map/Reduce - throw away orchestration, parallelism, scheduling, fault tolerance
    * Keep a DAG of deterministic transforms over partitioned data
