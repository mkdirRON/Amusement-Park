# ADR-0005: Event log with per-room sequence numbers

- **Status:** Accepted
- **Date:** 2026-10-28

## Context

Rooms need four things:

- live sync for every participant
- exact catch-up after a disconnect
- an audit trail and replay ("why did Bob's Muse suggest that?")
- a clean source for each agent's context

Timestamps make poor cursors. Two events can share a millisecond, and clocks on different machines disagree, so a timestamp cursor will miss or duplicate events.

## Decision

- Every change in a room is an **immutable event** appended to that room's log.
- Each event gets a per-room `seq` that only ever increases. The database enforces the ordering with a primary key on `(room_id, seq)`.
- Current state (chat, tasks, phase, whose turn it is) is **derived** by replaying events through a reducer.
- Clients send **commands**. The server checks each one against current state, and only valid commands produce events. Agents and humans use the same command path.
- Clients sync with a cursor, `since_seq`, and pages of results that include `has_more`.
- Private notes never enter the log (see ADR-0002).

## Alternatives considered

- **Ordinary CRUD tables:** simpler on day one, but live sync, catch-up and audit would all have to be built by hand.
- **Timestamp cursors:** rejected because they duplicate or miss events.
- **Offset pagination:** rejected. It shifts as the log grows, while a cursor stays stable.

## Consequences

- ✅ Catch-up is exact: "everything after #412."
- ✅ Replay and audit come almost free, and they make a strong demo.
- ✅ The database settles concurrent writes, because only one write can claim a given `seq`.
- ✅ Validation lives in one place, the command handler.
- ⚠️ Thinking in events takes practice.
- ⚠️ Event formats will change over time, so each event needs a version.
- ⚠️ Replaying a long log gets slow, so snapshots will be needed eventually.
