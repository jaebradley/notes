# [Zab: High-performance broadcast for primary-backup systems]()

## Introduction

* Each state change is incremenetal with respect to the previous state
* State changes cannot be applied in any arbitrary order
* State changes are idempotent and applying the same state change multiple times does not lead to inconsistencies as long as the application order is consistent with the delivery order
  * Guaranteeing at-least once semantics is sufficient and simplifies the implementation
