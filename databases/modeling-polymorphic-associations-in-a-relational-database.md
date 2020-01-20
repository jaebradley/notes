# [Modeling Polymorphic Associations in a Relational Database](https://hashrocket.com/blog/posts/modeling-polymorphic-associations-in-a-relational-database)

* Example is to design an access control system
  * Multiple resources with read, write, admin, etc. permissions
  * Permissions can be granted to a user or user groups
* Polymorphic join would use a `resource_type` and a `resource_id` combination (unique)
  * Limited data integrity due to lack of foreign key constraints
  * A resource can be missing an entry in this table and a row in the table can point to a missing resource
* Join Table Per Relationship Type
  * If the relationship types are `document`, `image`, etc. then there is table for each of these relationship types that combines an access control list id with a document id (or an image id, etc.)
  * Uses foreign key constraints so database can ensure that acl exists and relationship entity exists
  * No way to ensure that every entity for a given relationship type _has_ an acl
  * While uniqueness constraint can be enforced per table such that multiple entities for a given relationship type (like two documents) cannot share an acl, there's no way to prevent acl from being shared across entities for different relationship types (acl is incorrectly attributed to both a document and an image)
* Reverse Belongs-To
  * Include acl id on each resource so the `documents` table has an `acl_id` column that is a foreign key to the `acl` table
  * No way to ensure every acl record points to a resource
  * No way to prevent different resources from pointing to same acl
* Exclusive Belongs To (AKA Exclusive Arc)
  * acl table has foreign keys to all tables to which it can belong
  * So acl table would have a `document_id` column and an `image_id` column and a `file_id` column and a `report_id` column, etc.
  * It would implement a check that ensures the sum of all the ids is `1` (so that only one acl can reference a given entity)
  * Important to create partial unique indices on the acl table per entity id column where only non _null_ values are considered as this saves space
  * An acl cannot be orphaned since it must belong to one, and only one, entity
  * No way to enforce that every entity for a given resource type has an acl
* Author dismisses polymorphic joins for lack of data integrity guarantees
* Join table costs an extra table per relationship and doesn't improve on reverse belongs-to or exclusive belongs-to
* Of two belongs-to models, the main difference is whether the acl or resource entity must exist first to satisy foreign key constraint (reverse needs acl to exist first, exclusive needs entity to exist first)
  * Author finds that the resource is the master record and is generally more convenient and intuitive to create the resource first
* Issues with exclusive belongs-to
  * Wide tables may cause performance to suffer, but for PostgreSQL, nullable fields have a 1-bit per row overhead (rounded to nearest byte)
  * Combined with partial indices ignoring null values and performance may not be an issue
  * Adding a new resource table requires adding a column to the acl table which may cause a lot of contention / locking depending on how heavily-used the table is
    * Not a problem with PostgreSQL as nullable fields can be added quickly regardless of table size and check constraint can be updated without blocking concurrent usage
