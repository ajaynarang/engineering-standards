# ADR-0001: Split member and staff frontends

- **Status:** Accepted
- **Date:** 2026-02-19
- **Deciders:** Priya Raman, Elena Sousa, Aisha Khan
- **Handbook clauses:** §1.2, §6.2

## Context

The original teller tooling lived inside the member banking app behind role checks. Member
and staff surfaces evolve at different speeds, have different security postures, and are
owned by different squads. Every staff-tools release forced a full member-app regression.

## Decision

Member and staff experiences are separate applications with separate repositories and
owners. `member-web` (Angular) remains the member online-banking app, owned by Member
Experience. Staff tooling moves to a new `staff-portal` application (Next.js), owned by
Staff Tools. Rollout: stand up `staff-portal`, migrate member lookup and approvals, then
remove staff routes from `member-web`.

## Consequences

Two frontends to maintain and two design systems to keep roughly aligned — accepted. Each
squad releases independently; staff features stop riding member-app release trains; CODEOWNERS
routing becomes unambiguous per §6.2.
