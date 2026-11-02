# ADR-0018: Blockers and approvals

- **Status:** Accepted
- **Date:** 2026-10-28

## Context

During WORKING, agents act in the real world (see ADR-0006). They run into blockers such as logins, CAPTCHAs and information only a human has. Some actions, like paying, booking, sending or submitting, can't be undone.

Muse already notifies its owner and requires approval for purchases and messages it sends through its own connectors.

## Decision

**Action classes:**

| Class | Examples | Rule |
|---|---|---|
| Read | search, browse, read pages | Free |
| Reversible | drafts, holds, items in a cart, documents | Free; logged |
| Irreversible or external | pay, book, send, submit, post | Requires the **owner's** approval |

- **Group votes decide direction** (entering WORKING, accepting a plan). **Personal approvals cover consequences:** whoever's account or money is involved approves. One member's agent never spends another member's money.

**Blockers are handled notification-first, then timed out:**

1. An agent reports a blocker (`raise_blocker`), or the server detects a stall: no progress for N rounds.
2. Only the relevant human is notified.
3. A grace window starts. Other agents keep working on tasks that aren't blocked.
4. One reminder is sent.
5. If the window runs out, **only that task** is paused. If every task is blocked, the agents return a REPORT to the chat.

For the POC, notifications go through Muse's own channel to its owner (push notifications and the Approvals view). The Park doesn't need its own push notifications yet.

## Consequences

- ✅ Irreversible actions always have a human approving them.
- ✅ One blocked task doesn't stop the whole room.
- ✅ The POC reuses Muse's existing notifications.
- ⚠️ Grace windows and stall thresholds will need tuning.
- ⚠️ Stall detection is heuristic and may fire wrongly.
