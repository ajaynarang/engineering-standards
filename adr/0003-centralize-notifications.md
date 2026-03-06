# ADR-0003: Centralize member notifications in notifications-service

- **Status:** Accepted
- **Date:** 2026-03-16
- **Deciders:** Elena Sousa, Priya Raman, Tom Whitfield
- **Handbook clauses:** §4.5, §5.3

## Context

Payments sent its own transfer confirmations; statement generation had a dormant email
code path; staff tooling wanted alerts. Three senders meant three template styles, no
shared send log, and no single place to enforce §5.3's PII masking rules.

## Decision

One service — `notifications-service`, owned by Staff Tools — owns member communication:
templates, channel delivery (email, push, in-app), and the send log. Every other service and
batch job requests sends through its API per §4.5. Rollout: payments first (highest
volume), then wire statement-batch's statement-ready notices; direct sending is removed as
each caller migrates.

## Consequences

Notifications-service is a shared runtime dependency for both squads — its API stability
matters more than its feature velocity. In exchange: one template catalog, one auditable
send log, one enforcement point for PII masking on joint accounts.
