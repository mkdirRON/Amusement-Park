# ADR-0013: API versioning — `/v1/`, additive changes only

- **Status:** Accepted
- **Date:** 2026-10-31

## Context

Each member's connector is code that Muse wrote once, during setup (see ADR-0003). It doesn't update itself when our API changes. If we rename a field or remove an endpoint, every existing connector breaks until its owner asks Muse to rebuild it.

## Decision

- The API version is in the URL path: `/v1/...`.
- Within a version, only **additive** changes are allowed: new optional fields, new endpoints, and new event types that clients may ignore.
- Fields and endpoints are never renamed or removed within a version, and their meaning never changes.
- A breaking change means a new version, `/v2/`. `/v1/` keeps running during a migration period.
- The spec, connector doc, agent guide and setup prompt each name the version they describe.
- The connector doc tells clients to **ignore fields they don't recognize**.

## Alternatives considered

- **Versioning in a request header:** less visible to model-written scripts, and easier to forget.
- **No versioning:** every change risks breaking connectors that are already deployed.

## Consequences

- ✅ Existing connectors keep working while the API grows.
- ✅ Breaking changes become deliberate and visible.
- ⚠️ Old versions have to be kept running for a while.
- ⚠️ Design mistakes are hard to undo, so it pays to spend time on the spec up front (see ADR-0008).
