# ADR-0008: Spec-first OpenAPI contract

- **Status:** Accepted
- **Date:** 2026-10-31

## Context

Three things depend on the exact shape of the API:

1. the Go server
2. the TypeScript web UI
3. **each member's Muse connector**, which Muse writes from the spec at setup and which then never changes (see ADR-0013)

If those three ever disagree, Muses break. Generating an OpenAPI spec from Go code is less mature than in some other languages, and a spec produced as a side effect tends to get less design attention than it deserves.

## Decision

A hand-written OpenAPI 3 file, `api/openapi.yaml`, is the source of truth.

- Go types and the server interface are generated from it (oapi-codegen).
- TypeScript types for the web UI are generated from it.
- Muse builds its connector from it.
- Every change to the API starts as a change to the spec.
- Descriptions are written with AI readers in mind: each operation is labeled *read* or *write*, and every error lists what the caller should do next.

```
              api/openapi.yaml
        ┌──────────┼───────────┐
        ▼          ▼           ▼
   Go server   TS web UI   Muse connector
```

## Alternatives considered

- **Code-first, with the spec generated from Go annotations:** the spec becomes a byproduct and gets less deliberate design.
- **Spec and code maintained separately by hand:** they drift apart, and drift breaks Muses.

## Consequences

- ✅ One contract, so the server, the UI and Muse can't disagree.
- ✅ The API gets designed before it gets implemented.
- ✅ The spec doubles as the documentation Muse reads.
- ⚠️ The developer has to learn the OpenAPI format.
- ⚠️ Code generators come with their own limits and conventions.
- ⚠️ Regenerating code becomes a step in the workflow, and CI should check that generated code is up to date.
