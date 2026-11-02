# ADR-0007: Go for the server

- **Status:** Accepted
- **Date:** 2026-10-31

## Context

The server's work is mostly waiting: many long-poll requests held open at once, small JSON requests, and timers for turn deadlines. It doesn't do heavy computation.

The web UI will be TypeScript no matter what. The developer has programmed before but is building their first full end-to-end professional project, and wants the language choice to teach real engineering habits.

Candidates were TypeScript, Python, Go and C++.

## Decision

The server is written in Go, using the standard library where possible: `net/http`, `encoding/json`, `log/slog`, `crypto` and `testing`, plus a SQLite driver. The web UI is written in TypeScript.

## Alternatives considered

- **TypeScript (Node.js):** one language everywhere, with shared types and good real-time support. A strong option, set aside mainly because Go fits the many-waiting-requests workload and the learning goals better.
- **Python (FastAPI):** the best automatic OpenAPI generation. A weaker fit for thousands of cheap concurrent waiters, and it would still mean two languages.
- **C++:** its raw speed goes unused, since the server spends its time waiting on the network. Web libraries are clumsy, and memory bugs are a real risk.

## Consequences

- ✅ Goroutines make holding requests open cheap.
- ✅ Request contexts cancel cleanly when a client gives up, which is exactly what happens when a Muse script hits its 30-second timeout.
- ✅ Channels fit the "wake everyone waiting" pattern well.
- ✅ The server compiles to one binary, which makes moving from the laptop to a real host easy.
- ✅ A strong standard library and a culture of table-driven tests.
- ⚠️ Two languages (Go and TypeScript). Generating both sides' types from one spec reduces the cost (see ADR-0008).
- ⚠️ `if err != nil` error handling is verbose.
- ⚠️ Gotcha: the standard router (`ServeMux`) can redirect, for example from a path without a trailing slash to one with it. That breaks the no-redirects rule (ADR-0016), so register exact paths and test for 3xx responses.
- ⚠️ Gotcha: `http.Server.WriteTimeout` must be longer than the 20-second long-poll cap (ADR-0009). Otherwise the server cuts off its own waiting requests.
- ⚠️ The developer is new to Go.
