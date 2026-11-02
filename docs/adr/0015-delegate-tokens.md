# ADR-0015: Delegate tokens — scoped, hashed, entered on Muse's secure page

- **Status:** Accepted
- **Date:** 2026-10-29

## Context

The server is reachable from the public internet, so it must know exactly who is calling: for example, "Alice's Muse, acting for Alice, in room 42."

Testing showed the following:

- Muse handles bearer-token authentication correctly.
- Muse's secure credential flow gives the connector script only a **surrogate** token. Muse's credential service swaps in the real token only for requests to the **allowed host**, so the script never holds the real value.
- In an early test, a token typed into chat stayed in the conversation history.

## Decision

- Each combination of **member, room and client type** (agent or human) gets its own **delegate token**: random, scoped, expiring and revocable.
- The server stores **only a hash** of each token, never the token itself.
- Tokens are sent as `Authorization: Bearer <token>`.
- Users enter tokens **only on Muse's secure credential page, never in chat.** The setup prompt says so explicitly.
- Closing a room revokes all of its tokens. Any later call gets a `401` whose message tells Muse to remove its recurring check.
- Authentication ("who are you?", `401`) is kept separate from authorization ("are you allowed?", `403` or `409`).

## Alternatives considered

- **OAuth:** a better experience for users, but much more to build. Revisit after the POC.
- **One shared room password:** no identity per member and no way to revoke one person's access.
- **Storing tokens in plain text:** a database leak would hand out working tokens.

## Consequences

- ✅ A stolen surrogate is useless anywhere except our host.
- ✅ Each member's access can be revoked individually.
- ✅ A database leak doesn't expose usable tokens.
- ⚠️ Setup includes a manual copy-and-paste step.
- ⚠️ Issuing and revoking tokens needs a UI eventually. Manual is fine for the POC.
- ⚠️ Hashed tokens need a lookup design. Look up the "token prefix + hash" pattern.
