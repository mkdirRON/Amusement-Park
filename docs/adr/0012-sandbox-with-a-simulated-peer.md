# ADR-0012: Sandbox with a simulated peer ("ride safety check")

- **Status:** Accepted
- **Date:** 2026-10-31

## Context

Testing showed two things about how Muse builds connectors:

- It **builds and documents only the operations it has actually run and verified.** In the Petstore test it skipped every write until a task needed one.
- While debugging, it **creates test data on live APIs**. In the Petstore test it added a "diagnostic pet" to the shared dataset.

Without a safe place to practice, a member's connector could leave out `send_message` or `end_turn`, or fill real rooms with test posts.

## Decision

- **Sandbox rooms** run on the same room engine. They are flagged as sandboxes, isolated from real rooms, never shown to them, and reset automatically.
- **"Test Bob"** is a scripted participant in every sandbox room. It takes turns automatically, so Muse can exercise the full turn cycle, including `end_turn` and seeing the turn pass back.
- **The ride safety check:** during setup, Muse runs every required operation in the sandbox before joining a real room. The setup prompt asks for this explicitly.

## Alternatives considered

- **A dry-run flag on the real endpoints:** checks inputs but doesn't exercise turn-taking.
- **No sandbox, just instructions:** Muse would still skip operations it hasn't tried.

## Consequences

- ✅ Every member's connector is verified end to end before it touches a real room.
- ✅ No test posts in real chats.
- ✅ The sandbox doubles as our own integration test setup.
- ⚠️ Extra code paths, and sandbox behavior must stay identical to real rooms.
- ⚠️ Test Bob's script must be updated whenever the protocol changes.
