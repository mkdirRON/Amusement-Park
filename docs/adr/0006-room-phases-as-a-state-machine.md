# ADR-0006: Room phases as a state machine

- **Status:** Accepted
- **Date:** 2026-10-28

## Context

If agents are always on, a room is confusing, expensive and hard to stop. The product sketch describes a cycle: humans discuss, the group agrees to send the agents to work, the agents collaborate, and they report back to the chat.

An earlier draft (v0.2) had separate REVIEW and EXECUTING phases. Once the agents were also doing the work, those turned out to add steps without adding safety.

## Decision

Each room is a finite state machine:

```
DISCUSS ──(written goal + passing vote)──▶ WORKING
   ▲                                          │
   └──── REPORT posted to chat ◀──────────────┘
         (consensus · stuck · all blocked · budget/round limit · human interrupt)
```

- **Entering WORKING:** a member proposes a written goal, which becomes the agents' brief. Moving to WORKING requires a passing vote, unanimous for two people and configurable for groups.
- **During WORKING:** agents plan *and* act. Humans can watch in read-only mode and can interrupt. Agents can look things up in the chat history. Each agent can ask its own owner questions privately.
- **Tasks:** OPEN → CLAIMED → DONE, or → BLOCKED (see ADR-0018).
- The server enforces which commands are allowed in each phase.
- Risky actions are handled by owner approvals (ADR-0018), not by a separate phase.

## Alternatives considered

- **Always-on agents:** rejected. Unbounded cost, confusing, and hard to stop.
- **Separate REVIEW and EXECUTING phases (v0.2):** merged into WORKING. Approvals for each action give the same safety with fewer steps.

## Consequences

- ✅ Agents never start group work without the group's consent.
- ✅ Cost and runaway loops are bounded by phase limits.
- ✅ The state machine is pure logic and can be tested without a network, which makes it the first coding milestone.
- ⚠️ Votes add friction for small tasks.
- ⚠️ Rules for larger groups (quorum, abstentions) need careful design.
- ⚠️ The POC may implement only part of this: round-robin turns, possibly without votes.
