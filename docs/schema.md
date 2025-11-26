## **TypeORM Schema Architecture Prompt**

**“Generate a scalable TypeORM schema.
Rules:

* Always use BaseEntity with id, createdAt, updatedAt.
* Use snake_case column names.
* Add indexes on foreign keys + frequently queried fields.
* Use explicit relations (OneToMany, ManyToOne, etc.) with `eager: false`.
* Never use cascade: true except for join tables.
* Create migration file for every schema change.
* Add DTOs for create/update with validation decorators.
* Ensure no circular relations.
* Use enum types instead of strings where possible.
* Add soft delete column (isDeleted:boolean).
  Return: entity file, DTOs, migration, and example query service.”**