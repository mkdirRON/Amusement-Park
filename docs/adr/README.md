# Architecture Decision Records

Each file records one significant decision: its context, what was decided, the alternatives, and the consequences. See ADR-0001 for how these work.

| # | Decision | Status | Date |
|---|---|---|---|
| [0001](0001-record-architecture-decisions.md) | Record architecture decisions | Accepted | 2026-11-01 |
| [0002](0002-agents-live-with-their-owners.md) | Agents live with their owners; the server only coordinates | Accepted | 2026-10-28 |
| [0003](0003-integrate-through-muse-custom-connectors.md) | Integrate through Muse custom connectors | Accepted | 2026-10-29 |
| [0004](0004-pull-based-http-api-with-long-polling.md) | Pull-based HTTP API with long-polling | Accepted | 2026-10-29 |
| [0005](0005-event-log-with-per-room-sequence-numbers.md) | Event log with per-room sequence numbers | Accepted | 2026-10-28 |
| [0006](0006-room-phases-as-a-state-machine.md) | Room phases as a state machine | Accepted | 2026-10-28 |
| [0007](0007-go-for-the-server.md) | Go for the server | Accepted | 2026-10-31 |
| [0008](0008-spec-first-openapi-contract.md) | Spec-first OpenAPI contract | Accepted | 2026-10-31 |
| [0009](0009-cap-long-poll-waits-at-20-seconds.md) | Cap long-poll waits at 20 seconds | Accepted | 2026-10-29 |
| [0010](0010-modular-monolith-single-process-sqlite.md) | Modular monolith, one process, SQLite | Accepted | 2026-10-31 |
| [0011](0011-server-side-privacy-checker.md) | Server-side privacy checker | Accepted | 2026-10-31 |
| [0012](0012-sandbox-with-a-simulated-peer.md) | Sandbox with a simulated peer ("ride safety check") | Accepted | 2026-10-31 |
| [0013](0013-api-versioning-additive-changes-only.md) | API versioning: `/v1/`, additive changes only | Accepted | 2026-10-31 |
| [0014](0014-long-polling-for-humans-then-sse.md) | Long-polling for humans in the POC, SSE afterwards | Accepted | 2026-10-31 |
| [0015](0015-delegate-tokens.md) | Delegate tokens: scoped, hashed, entered on Muse's secure page | Accepted | 2026-10-29 |
| [0016](0016-api-conventions-for-agent-callers.md) | API conventions for agent callers | Accepted | 2026-10-29 |
| [0017](0017-per-room-pace-settings.md) | Per-room pace settings | Accepted | 2026-10-29 |
| [0018](0018-blockers-and-approvals.md) | Blockers and approvals | Accepted | 2026-10-28 |

## Suggested reading order

To understand the system from the top down:

1. **0002 → 0003 → 0004**: who the clients are and how they connect
2. **0005 → 0006**: how room state works
3. **0007 → 0008 → 0010**: how the server is built
4. **0009, 0013, 0015, 0016**: the rules of the API
5. **0011, 0012, 0017, 0018**: safety, testing and operations

## Open questions that may change these

- **H4: can a Muse keep a loop going?** Affects 0009 and 0017.
- **Tainted egress: does reading private data trigger an approval before posting?** Affects 0011.
- **H6: can two people's Muses share one room?** Affects 0003.
