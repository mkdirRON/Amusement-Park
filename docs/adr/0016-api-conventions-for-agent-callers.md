# ADR-0016: API conventions for agent callers

- **Status:** Accepted
- **Date:** 2026-10-29

## Context

Our main callers are scripts that Muse writes and runs. Testing showed:

- The scripts **refuse redirects**.
- When an API errored, Muse **invented its own IDs** to work around it.
- **Error bodies reach the model**, printed as `HTTP {code}: {body}`.
- Muse **retries** after timeouts, and checks the current state first.
- Petstore returned a `500` (server fault) for what was really a missing field (caller fault), which sent Muse on a detour.

## Decision

Every endpoint follows these conventions:

1. **Never redirect.** Use exact paths and HTTPS only, and test that no response is ever a 3xx.
2. **The server generates every ID.** Clients never supply them.
3. **Accurate status codes:** `400` for invalid input, `401` for an unknown caller, `403` for a forbidden action, `404` for something missing, `409` for the wrong state (such as "not your turn"), `429` for too many requests. `5xx` is used only for genuine server faults.
4. **Errors that teach:** every error body has a machine-readable code and a message that says what to do next.
5. **Idempotency keys on every write.** A retry with the same key returns the original result instead of repeating the action.
6. **Size limits:** messages up to about 8 KB, matching Muse Code's limit for messages between sessions.
7. **Rate limits** return `429` with guidance ("use wait_seconds instead of polling").
8. **Every operation is described and labeled** *read* or *write* in the spec (see ADR-0008).

## Consequences

- ✅ Predictable behavior for clients written by a model.
- ✅ Error messages steer agents toward the right fix instead of a workaround.
- ✅ Retries are safe.
- ⚠️ Each endpoint takes more design work.
- ⚠️ Idempotency keys must be stored and eventually expired.
