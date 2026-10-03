# College Advisor

A college, scholarship, and opportunity advising platform for African
students: discovery, eligibility checking, matching, application tracking,
and an AI adviser, backed by an admin system for managing institutions,
programs, scholarships, and opportunities without touching the database
directly.

## Status

Early scaffolding. See `docs/ARCHITECTURE.md` and `docs/DECISIONS.md` for
the current state of the system and why things are structured the way
they are.

## Technology

- **Backend:** Rust (Cargo workspace), [Axum](https://github.com/tokio-rs/axum)
- **Database:** PostgreSQL
- **Frontend:** TypeScript/React (student-facing and admin)
- **AI:** Provider-agnostic layer; structured data and deterministic rules
  are authoritative, AI explains and converses rather than inventing facts

## Repository layout

```
apps/        Deployable binaries (api, worker)
crates/      Domain libraries (auth, students, scholarships, etc.)
frontend/    Student and admin web apps
migrations/  Versioned database migrations
docs/        Architecture, database, API, security, development docs
tests/       Cross-crate / integration tests
```

Crates and apps are created as each phase of the build requires them,
not all at once, see `docs/DECISIONS.md` for the build philosophy.

## Development

See `docs/DEVELOPMENT.md` for local setup once the API and database
layers exist.

## Build checks

```bash
cargo fmt --check
cargo check --workspace
cargo test --workspace
cargo clippy --workspace
```
