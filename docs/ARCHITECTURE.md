# Architecture

## Purpose

College Advisor helps students discover institutions, academic programs,
scholarships, and other opportunities, check their eligibility, get
matched to relevant options, track applications, and get AI-assisted
advising. Administrators manage all of this content and review
automatically discovered opportunities, without needing direct database
access.

The system is designed from the start for maintainability, security,
reliability, and scale, since it is expected to eventually serve a large
number of students against a large database of institutions, programs,
and opportunities.

## Request flow

Business logic never lives in an HTTP handler. Every request follows the
same layering:

```
HTTP request
  -> API handler        (apps/api) — parses/validates the request, calls a service
  -> service layer       (crate)    — orchestrates a use case
  -> domain logic         (crate)    — business rules, independent of HTTP or SQL
  -> database             (via a repository/query layer)
  -> response
```

A handler should never contain the logic that decides, for example,
whether a student is eligible for a scholarship. That belongs in the
`eligibility` domain, called from a service, and only *used* by the
handler.

## Workspace layout

The repository is a single Cargo workspace. Crates and apps are created
when a phase actually needs them, not generated up front. The intended
eventual shape:

```
apps/
  api/            HTTP server (Axum)
  worker/         Background jobs (notifications, crawling, etc.)
crates/
  common/         Shared types/utilities used across crates
  auth/           Authentication & authorization
  users/          Base user accounts
  students/       Student profiles
  institutions/   Universities, colleges, TVET institutions
  programs/       Academic programs
  scholarships/   Scholarships and other funded opportunities
  eligibility/    Deterministic eligibility rules engine
  matching/       Recommendation/matching logic
  applications/   Application tracking
  advising/       Human + AI advising workflows
  ai/             AI-provider-agnostic integration layer
  crawler/        Automated opportunity discovery
  notifications/  Deadline/status notifications
frontend/
  student/        Student-facing web app (TypeScript/React)
  admin/          Admin dashboard (TypeScript/React)
migrations/       Versioned SQL migrations
tests/            Cross-crate integration tests
docs/             This documentation
```

**Current state (Phase 0):** only `crates/common` exists, as an empty
placeholder crate, created solely so the Cargo workspace has a member
(see `DECISIONS.md`, ADR 0003). No other crate, app, or frontend exists
yet.

## Domain separation

Each major domain listed above is expected to eventually become its own
crate, but a crate is only created once its domain has meaningful
independent logic, not for theoretical purity. Before adding new logic
anywhere, the existing crates are checked first so there is exactly one
authoritative implementation of each piece of business logic.

## AI layer

The AI layer is provider-agnostic and subordinate to structured data:

- Deterministic facts (eligibility rules, deadlines, requirements, fees)
  come from the database and rule engine, never from an AI model's
  memory.
- AI is used for explanation, natural-language advising, summarization,
  extraction, classification, and conversation.
- AI-generated answers are grounded in platform records wherever
  possible, and the integration is built so the underlying AI provider
  can be swapped without touching the rest of the system.

## Admin access

Administrators manage institutions, programs, scholarships, opportunities,
eligibility rules, students, advisors, and system configuration through
the admin frontend and its API endpoints, not by editing the database
directly. This is a binding product requirement, not just a convenience:
every piece of content an admin needs to add or change must have a
corresponding admin-facing API route and UI, from the phase that
introduces that content type onward.

## Status

Phase 0 (repository foundation) is in progress. See the root `README.md`
for build commands and `DECISIONS.md` for the architectural decisions
made so far.
