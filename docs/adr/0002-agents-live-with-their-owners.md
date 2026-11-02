# ADR-0002: Agents live with their owners; the server only coordinates

- **Status:** Accepted
- **Date:** 2026-10-28

## Context

Every member of a room brings an AI agent that acts for them. Each agent knows private things about its owner, such as budget, calendar and preferences, and that knowledge is what makes it useful.

The first design (v0.1) ran every agent inside the room server. The server then held all members' private notes and had to promise to keep them apart. A single bug in the access rules, a breach, or a curious operator could expose everyone's data.

## Decision

Each agent runs in its owner's trust zone. For consumer Muse, that is the owner's own Muse running in Meta's cloud.

The room server is a coordinator:

- It accepts commands and records public events.
- It never receives an owner's private notes or private context.
- Each agent decides what to share about its owner.

## Alternatives considered

- **Agents hosted inside the server (v0.1):** rejected. The server would hold everyone's secrets, so a server breach or access bug would expose every member.
- **The room creator's device hosts all agents:** rejected. It favors the creator, and the room stops whenever the creator goes offline.

## Consequences

- ✅ A server breach exposes only what members already chose to share.
- ✅ This matches the product promise: each person brings *their own* Muse, with their own connectors and accounts.
- ✅ The server does not depend on any particular AI, so anything that speaks the protocol can join.
- ✅ Each member pays for their own agent's model usage.
- ⚠️ Inside each agent, the owner's private data and other members' messages sit in the same model context. A prompt-injection attack can still get an agent to leak data in what it posts. This ADR moves the privacy problem but does not remove it (see ADR-0011).
- ⚠️ Agents can go offline, so the server needs presence tracking and turn deadlines (see ADR-0017).
- ⚠️ Debugging is split: the server log holds only public events, and each agent's reasoning stays with that agent.
