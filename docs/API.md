# API

## Versioning

All API routes are versioned under a path prefix:

```
/api/v1/...
```

Breaking changes get a new version prefix rather than silently changing
behavior under the existing one.

## Conventions

Predictable REST-style routes are used unless there's a strong reason
not to, for example:

```
GET    /api/v1/students/:id
POST   /api/v1/students
PATCH  /api/v1/students/:id
GET    /api/v1/scholarships
GET    /api/v1/programs
```

- Response and error formats are consistent across all endpoints (the
  exact envelope shape will be defined and documented here in Chunk 1.1
  when the first real endpoint is built, rather than invented now with
  no endpoint to validate it against).
- All external input is validated server-side. The client is never
  trusted, regardless of what the frontend already checked.
- Authorization is enforced server-side on every route that needs it,
  never assumed from the frontend's behavior.

## Current state

No API exists yet. The first endpoint, `GET /health`, is introduced in
Chunk 1.1 (Phase 1), returning:

```json
{ "status": "ok" }
```

This document will be expanded with the actual response/error envelope
shape, authentication header conventions, and a running list of
endpoints as each phase adds them.
