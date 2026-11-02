# ADR-0001: Record architecture decisions

- **Status:** Accepted
- **Date:** 2026-11-01

## Context

Amusement Park is built to production standards and as a portfolio piece. Most of its architecture came out of design discussions and hands-on experiments with Meta's Muse agent in October 2026. Many decisions rest on evidence (timeouts, permission behavior, how Muse builds connectors) that is easy to forget, and that may change as Meta updates Muse.

Code shows *what* was built, not *why*. Reviewers, and the author six months from now, need the reasoning and the rejected alternatives.

## Decision

Each significant architectural decision gets an Architecture Decision Record in `docs/adr/`.

- Format: Title, Status, Date, Context, Decision, Alternatives considered, Consequences.
- Files are numbered in sequence (`0001-…`, `0002-…`), and numbers never change.
- An accepted ADR is never rewritten. A changed decision gets a new ADR that says "Supersedes ADR-NNNN", and the old one is marked "Superseded by ADR-MMMM".
- A decision is significant if it is hard to reverse, affects more than one component, or depends on external evidence that could change.

## Alternatives considered

- **Wiki or notes outside the repo:** these drift out of sync with the code and disappear when the tool changes.
- **Commit messages only:** the reasoning ends up scattered across history, with no room for the alternatives.
- **No records:** the reasoning is lost and the same debates get repeated.

## Consequences

- ✅ The reasoning stays next to the code, versioned with it.
- ✅ Reviewers can follow how the design evolved.
- ✅ When Muse's behavior changes, the ADRs built on the old behavior are easy to find.
- ⚠️ Writing one takes 10 to 20 minutes per decision.
- ⚠️ Deciding what counts as significant takes judgment, and some decisions will slip through unrecorded.
