# Security

Security is treated as part of the architecture from Phase 0 onward, not
a feature added near the end. The following principles are binding on
every future chunk; a chunk that touches one of these areas is expected
to actually follow the relevant principle, not defer it.

## Principles

- **Password hashing** — passwords are never stored in plaintext; only
  salted hashes, once accounts exist (Phase 3).
- **Authentication & authorization** — enforced server-side on every
  route that needs it. The frontend's behavior is never treated as an
  access control mechanism.
- **Role-based permissions** — admin vs. student vs. advisor capabilities
  are distinguished server-side.
- **Input validation** — all external input is validated before use,
  regardless of what the client claims to have already checked.
- **SQL injection protection** — all database access uses parameterized
  queries; no hand-built SQL strings from user input.
- **Rate limiting** — applied where appropriate, particularly on
  authentication and any public-facing write endpoints.
- **Secure cookies/tokens** — session/auth tokens are handled with
  appropriate flags (HttpOnly, Secure, SameSite) once sessions exist.
- **CSRF protection** — applied where applicable, depending on the final
  auth mechanism chosen.
- **Audit logging** — sensitive actions (admin changes to records,
  authentication events, data exports) are logged once those actions
  exist.
- **Secrets management** — no credentials are ever committed to GitHub.
  `.env` is git-ignored; `.env.example` holds variable names only. API
  keys for external services (e.g. the AI provider) are read from
  environment configuration, not hard-coded or stored in the database
  without a deliberate encrypted-secret design.
- **Document handling** — uploaded application documents are handled
  carefully once that feature exists (Phase 18/19): access-controlled,
  not publicly readable by guessable URL.
- **Personal information** — student personal and academic data is
  treated as sensitive throughout; access is restricted to what a given
  role actually needs.

## Current state

No authentication, authorization, or data storage exists yet (Phase 0).
This document will be updated with concrete implementation details
(hashing algorithm, token format, rate-limit thresholds, etc.) as each
relevant phase lands, so it never drifts ahead of what's actually built.
