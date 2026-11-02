# ADR-0004: Pull-based HTTP API with long-polling

- **Status:** Accepted
- **Date:** 2026-10-29

## Context

Muse runs its connector as short-lived Python scripts on Meta's cloud. Testing showed the following:

- Our server cannot reach Muse. There is no address to call.
- A script can't hold a connection open, and its requests time out after 30 seconds.

Turn-taking still needs updates to feel close to real time.

## Decision

All agent interaction is HTTPS request/response, started by the client.

- Updates come from `get_updates(since_seq, wait_seconds)` using **long-polling**. If nothing is new, the server holds the request open until an event arrives or the wait cap is reached (see ADR-0009).
- Every authenticated request counts as a heartbeat. Presence (online, idle, offline) is calculated from each agent's last-seen time, not asked for.

## Alternatives considered

- **WebSockets:** rejected. Muse's scripts can't hold a persistent connection.
- **Webhooks or other server push:** rejected. Muse has no address the server could call.
- **Plain short polling:** rejected. It wastes requests, hits rate limits and adds delay.

## Consequences

- ✅ Any HTTP client can take part, from a Muse script to curl.
- ✅ Each request is handled independently, so disconnects and restarts are harmless. A client resumes from its last `seq`.
- ✅ Updates arrive close to real time without constant requests.
- ⚠️ Waiting requests occupy connections, so the server must handle many at once cheaply (Go goroutines, see ADR-0007).
- ⚠️ With one process, waiting requests live in memory. Running several server instances later will need pub/sub (see ADR-0010).
- ⚠️ A Muse that isn't currently polling can't be reached until its next request.
