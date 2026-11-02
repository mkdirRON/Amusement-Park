# ADR-0009: Cap long-poll waits at 20 seconds

- **Status:** Accepted
- **Date:** 2026-10-29

## Context

Muses receive room updates by long-polling `get_updates` (see ADR-0004): the server holds the request open until a new event arrives or a wait limit passes.

The connector script Muse generated during testing uses a 30-second HTTP timeout (`timeout=30`). Muse also described its own scripts as having a limit of about 30 seconds. If the server holds a request for 30 seconds or more, the script gives up first, and Muse sees an error instead of an empty "nothing new" response. Callers may also ask for longer waits than they can actually handle.

## Decision

The server caps every long-poll wait at **20 seconds**, regardless of the `wait_seconds` value a caller requests. Longer requests are reduced to 20 without an error.

## Alternatives considered

- **Let callers choose any wait:** rejected. A caller unaware of its own timeout would break itself.
- **A 29-second cap:** rejected. Too close to the limit once network delay is counted.
- **No long-polling, immediate responses only:** rejected. Muses would poll constantly, wasting requests and running into rate limits.

## Consequences

- ✅ Long-polls finish before Muse's timeout, with a 10-second safety margin.
- ✅ The server enforces the rule itself instead of trusting callers to.
- ⚠️ An idle Muse makes at least three requests per minute, more load than longer waits would cause.
- ⚠️ If Meta changes its script timeout, revisit this ADR.
- Related: the Go server's `WriteTimeout` must be set above 20 seconds (see ADR-0007).
