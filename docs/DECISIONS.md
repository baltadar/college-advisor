# Architecture Decision Records

Each entry records a real decision actually made while building the
platform, numbered in order. This file is append-only, old decisions are
not deleted when superseded, a new entry supersedes them explicitly.

---

## ADR 0001: Technology stack

**Date:** Phase 0, Chunk 0.1
**Context:** The project needs a backend language, HTTP framework, and
database, as specified in the master build instructions.
**Decision:** Rust (Cargo workspace) + Axum + PostgreSQL for the backend,
TypeScript/React for the frontend, with a provider-agnostic AI
integration layer.
**Consequences:** All future chunks build on this stack. Any deviation
requires a new ADR explaining why.

---

## ADR 0002: Workspace edition set to 2021, not 2024

**Date:** Phase 0, Chunk 0.1
**Context:** The build environment used so far only has Rust 1.75.0
available (installed via the OS package manager; `rustup`'s usual
download host isn't reachable from that sandbox). Edition 2024 requires
a newer `rustc` than 1.75.
**Decision:** Set `edition = "2021"` in the workspace `Cargo.toml` for
now, rather than silently targeting 2024 and having builds fail.
**Consequences:** No 2024-only language features are available yet. This
should be revisited (a one-line change plus a compatibility check) once
development moves to a newer toolchain. Not a structural decision, just
an environment constraint, safe to change later.

---

## ADR 0003: `crates/common` created early, as an empty placeholder

**Date:** Phase 0, Chunk 0.1
**Context:** Chunk 0.1 explicitly forbids implementing application
functionality, but `cargo check --workspace` fails outright on a
workspace with zero resolved members, it's a hard Cargo error, not a
style preference.
**Decision:** Create `crates/common` now, as a crate containing only a
module-level doc comment, no types, no functions. `common` is already
named in the master instructions' domain list (Section 8) as an
eventually-needed shared crate, so this isn't introducing an unplanned
component, just creating its shell slightly ahead of schedule to satisfy
a mechanical constraint.
**Consequences:** `crates/common` has no real API yet. Its actual
contents (shared error types, common DTOs, etc.) are decided later, by
whichever chunk first needs to share something across crates, not by
this decision.
