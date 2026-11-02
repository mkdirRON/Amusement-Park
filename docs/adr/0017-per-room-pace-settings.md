# ADR-0017: Per-room pace settings

- **Status:** Accepted
- **Date:** 2026-10-29

## Context

Muse describes two ways it can keep participating in a room. Both are self-reported and not yet verified (H4):

- **Background tasks** that can run for a long time. These suit real-time rounds.
- **Recurring jobs** that run roughly every N minutes, give or take a few minutes, and **never end on their own**.

With one fixed turn deadline, one of these modes always fails. A Muse checking in every 10 minutes, give or take 3, would never make a 2-minute deadline.

## Decision

- Each room has a **pace**:
  - **Live:** turn deadlines of about 2 to 3 minutes. Muses run background loops.
  - **Check-in:** turn deadlines of about 20 to 30 minutes. Muses run recurring jobs.
- The turn clock uses the room's pace. When a turn runs out, a `TurnExpired` event is recorded and the room moves on.
- `get_room_state` reports the room's status: `active`, `paused` or `closed`.
- The agent guide tells Muse to **delete its recurring check once a room is closed**. Revoked tokens back this up (see ADR-0015).

## Alternatives considered

- **One fixed deadline:** rejected, because it breaks one of the two modes.
- **No deadlines:** rejected. One absent agent would freeze the room.

## Consequences

- ✅ Supports both real-time sessions and rooms that run for days.
- ✅ Absent agents can't freeze a room.
- ⚠️ One more setting for room creators.
- ⚠️ The deadline values are guesses until the loop test (H4) is done.
