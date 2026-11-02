# ADR-0010: Modular monolith, one process, SQLite

- **Status:** Accepted
- **Date:** 2026-10-31

## Context

One developer is building this. The POC runs on a laptop, and early scale is a handful of rooms. The design still needs clean boundaries, so it can grow later without a rewrite.

## Decision

The server runs as **one Go process** made of separate modules with clear responsibilities:

| Module | Responsibility |
|---|---|
| API layer | HTTP only: routing, auth, validation. Turns requests into commands |
| Room engine | Pure room logic: commands → state machine → events. Knows nothing about HTTP |
| Storage | SQLite: rooms, members, hashed tokens, events, idempotency keys, held messages |
| Turn clock | Enforces turn deadlines by sending commands to the engine |
| Waiters | Holds long-poll requests open and wakes them when events are appended |
| Privacy checker | Can veto or hold a message before it is appended (ADR-0011) |
| Sandbox | The same engine running isolated rooms (ADR-0012) |
| Docs | Serves the spec, connector doc, agent guide and setup prompt |

**Rule:** every state change goes through the room engine's command path. The turn clock, and later the ride-operator agent, are ordinary command sources with no shortcuts around the engine.

## Alternatives considered

- **Microservices:** they add network calls, deployment work and new ways to fail, with no benefit at this scale.
- **Postgres from day one:** more to install and run, for capacity the POC doesn't need.

## Consequences

- ✅ Simple to run, debug and deploy.
- ✅ The room engine can be tested on its own.
- ✅ Clean boundaries turn later splits into refactors, not rewrites.
- ⚠️ Waiters live in memory, so this works for one server instance only. More instances will need pub/sub (Postgres LISTEN/NOTIFY or Redis).
- ⚠️ SQLite allows one writer at a time, which is fine for the POC.
- ⚠️ Running on a laptop means a room stops when the laptop sleeps. Accepted for the POC; revisit when it becomes a problem.
