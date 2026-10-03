# Database

## Engine

PostgreSQL is the single authoritative persistent store for the
platform. No other database is introduced without updating this
document and explaining why.

## Principles

- **Migrations only.** Schema changes are made exclusively through
  version-controlled migration files in `migrations/`. Production
  schema is never modified by hand.
- **Relational structure over JSON blobs.** Where data has real
  structure (a student's academic record, a scholarship's eligibility
  criteria), it is modeled with proper tables and columns, not stuffed
  into a JSON column, so it can be queried, indexed, and validated
  properly. JSON is reserved for genuinely unstructured or
  provider-specific data.
- **Foreign keys** are used wherever a real relationship exists, so the
  database itself enforces referential integrity rather than relying on
  application code alone.
- **Indexes** are added deliberately, based on actual query patterns, not
  speculatively on every column.
- **Timestamps** (`created_at`, `updated_at`, and domain-specific ones
  like `deadline_at`) are used consistently and stored in UTC.
- **Identifiers.** The platform uses UUIDs as primary keys across
  tables, chosen so IDs are safe to expose in URLs/APIs and don't leak
  sequence information. This is the default; a documented exception
  would be needed to deviate from it for a specific table.
- **Passwords** are never stored in plaintext; only salted password
  hashes are stored, once authentication is introduced (Phase 3).
- **Secrets/API keys** are not stored in the database unless there is a
  deliberate, documented encrypted-secret architecture. Until then, keys
  the platform needs (e.g. for the AI provider) live in environment
  configuration, not in a table.

## Current state

No schema exists yet. No migrations have been written. This will be
introduced starting in Phase 2 (core data model), at which point this
document will be updated with:

- the migration tool/approach chosen
- the initial table list and their relationships
- the UUID generation strategy used in practice
- the indexing decisions made and why

## Open decisions (not yet made)

- Which migration tool/library the `api`/`worker` apps will use to run
  migrations (chosen when Phase 2 actually needs it, not before).
- The specific normalization approach for eligibility criteria
  (structured columns vs. a dedicated rules table) — decided alongside
  the `eligibility` crate in its own phase.
