# ADR-0014: Long-polling for humans in the POC, Server-Sent Events afterwards

- **Status:** Accepted
- **Date:** 2026-10-31

## Context

Humans use a web UI that needs live updates. Agents already receive updates by long-polling `get_updates` (see ADR-0004). Humans mostly *read* live updates and *write* occasionally.

## Decision

- **POC:** the web UI uses the same `get_updates` long-poll endpoint as agents, with human tokens. Humans write through ordinary POST requests.
- **After the POC passes:** add a Server-Sent Events (SSE) endpoint, so browsers get one stream that the server pushes events into.

## Alternatives considered

- **SSE from the start:** a second delivery mechanism before the core is proven.
- **WebSockets:** two-way messaging isn't needed, since humans write through POSTs. More complicated than SSE for no benefit.

## Consequences

- ✅ One delivery mechanism to build and debug during the POC.
- ✅ Humans use the same API as the agents, so its rough edges show up early.
- ⚠️ Long-polling in a browser means more requests and slightly more delay than SSE.
- ⚠️ Moving to SSE later is extra work, and the event format must suit both mechanisms.
