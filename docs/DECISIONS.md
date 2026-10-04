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

---

## ADR 0004: Lint and formatting policy, kept deliberately minimal for now

**Date:** Phase 0, Chunk 0.3
**Context:** The master instructions require `cargo fmt --check` and
`cargo clippy --workspace` to pass, with "appropriate compiler
warnings," but with no real application code yet, there's nothing to
validate an opinionated, detailed lint policy against.
**Decision:** Two rules only, chosen because they're directly justified
by principles already stated in `SECURITY.md`/the master instructions
rather than invented here:
- `unsafe_code = "deny"` (workspace Rust lint) — the project avoids
  unsafe Rust unless there's a documented, compelling reason; `deny`
  still allows a justified, visible `#[allow(unsafe_code)]` at a
  specific call site, rather than requiring a workspace-wide exception.
- `clippy::all = "warn"` (workspace Clippy lint) — made explicit rather
  than left as an implicit default, so the intent is documented.
Formatting is pinned via `rustfmt.toml` (`edition = "2021"`,
`max_width = 100`) so "correctly formatted" doesn't depend on whichever
rustfmt defaults happen to ship locally or in CI.
**Consequences:** Stricter lint groups (`clippy::pedantic`,
`clippy::unwrap_used`, etc.) are deliberately not enabled yet. Adding
them now, before real business logic exists, risks tuning them against
nothing. Revisit once a real crate (e.g. `auth` or `eligibility`) has
enough code to judge whether a stricter policy is actually useful or
just noisy, and log that as a new ADR rather than editing this one.

---

## ADR 0005: CI toolchain pinned; Clippy warnings fail CI but not local dev

**Date:** Phase 0, Chunk 0.4
**Context:** GitHub Actions' `ubuntu-latest` runners ship a recent stable
Rust via `rustup`, which would be newer than the 1.75.0 available in
local development (ADR 0002). Running different versions locally and in
CI risks "works on my machine" drift, including Clippy lint sets
changing between versions.
**Decision:**
- Pin CI to the exact same toolchain as local dev, Rust 1.75.0 with the
  `rustfmt` and `clippy` components, via `dtolnay/rust-toolchain@1.75.0`.
- Run `cargo clippy --workspace --all-targets -- -D warnings` in CI,
  turning warnings into failures, while local development only runs
  plain `cargo clippy --workspace` (warnings visible, not blocking).
**Consequences:** Local iteration stays fast and unblocked by lint
nitpicks; nothing with a Clippy warning can reach `main` unnoticed.
When the toolchain is deliberately upgraded (see ADR 0002), this
workflow's pinned version must be updated in the same change, or CI and
local dev will silently drift apart again.
