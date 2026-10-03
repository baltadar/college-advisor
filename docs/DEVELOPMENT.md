# Development

## Toolchain

- **Rust:** 1.75.0 (installed via the build environment's package
  manager). Workspace edition is set to **2021**, not the newer 2024
  edition, because edition 2024 requires a newer `rustc` than is
  currently available in the build environment used for this project so
  far. See `DECISIONS.md` ADR 0002. This can be revisited once a newer
  toolchain is in use.
- **Cargo workspace:** the whole backend is one workspace (`Cargo.toml`
  at the repo root), so all crates/apps share a lockfile and build
  output.

## Build commands

From the repository root:

```bash
cargo check --workspace     # type-check everything, fast
cargo test --workspace      # run all tests
cargo fmt --check           # formatting check
cargo clippy --workspace    # linting
```

All four are expected to pass before a chunk is considered done. Formatting
rules live in `rustfmt.toml` at the repo root. Lint policy lives in the
`[workspace.lints]` tables in the root `Cargo.toml`; each crate opts in
with `[lints]\nworkspace = true` in its own `Cargo.toml`, a new crate that
forgets this line will silently not be linted, so this is worth checking
when adding one.

## Environment configuration

Copy `.env.example` to `.env` and fill in real values locally. `.env` is
git-ignored and must never be committed. `.env.example` only ever
contains variable *names*, never real credentials.

Current variables (see `.env.example`):

- `DATABASE_URL` — PostgreSQL connection string (not used yet, no
  database layer exists until Phase 2)
- `APP_ENV` — `development` | `staging` | `production`
- `SERVER_HOST`, `SERVER_PORT` — API server binding (not used yet, no
  server exists until Phase 1)
- `JWT_SECRET` (commented out) — set once authentication is introduced
  in Phase 3

## Local database

Not needed yet. This section will be filled in once Phase 2 introduces
the database layer and migrations, with concrete setup steps
(PostgreSQL version, how to create the local database, how migrations
are run).

## Repository layout

See `ARCHITECTURE.md` for the full intended structure and the current
state of what actually exists so far.
