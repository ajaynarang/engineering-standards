# Meridian Credit Union — Engineering Handbook

This handbook is the standard of record for how Meridian builds and changes member-facing
software. Rules use MUST / SHOULD in the RFC-2119 sense. Every clause is numbered; cite
clauses by number in ADRs, pull requests, and reviews.

## 1. Purpose and values

### 1.1 Member-first correctness

Meridian moves members' money. Correctness outranks delivery speed, novelty, and elegance.
When a trade-off touches balances, transfers, or statements, the safe option wins and the
decision is written down.

### 1.2 Boring technology

We choose proven, well-understood tools over new ones. A technology enters the stack through
an ADR (see 6.3), not through a side project that quietly became load-bearing.

### 1.3 Auditability by default

Every material change to member data or member-facing behavior MUST be traceable to a
reviewed change set and, where applicable, an approval. If an examiner asks "who changed
this and why," the answer is a link, not an interview.

## 2. Definition of done

A change is done when all of the following hold.

### 2.1 Peer review

Every change MUST be reviewed by at least one engineer who did not author it. Owners listed
in CODEOWNERS review changes to the paths they own.

### 2.2 Tests required

New behavior ships with tests that fail without it. Bug fixes ship with a regression test.
Repositories without a test harness MUST NOT take on new feature work until one exists.

### 2.3 Documentation

READMEs describe what a service does, how to run it, and who owns it. A change that alters
any of those MUST update the README in the same change set.

### 2.4 Contract sync

Any change to a service's HTTP surface MUST land with a matching update to its contract in
`api-contracts` in the same change set. The contract is the interface of record; code that
disagrees with it is wrong by definition.

## 3. Coding standards

### 3.1 Java services

Java services target Java 17 and Spring Boot 3.x, built with Maven. Constructor injection
only; no field injection. Controllers stay thin — domain logic lives in services. In-memory
state is acceptable only in mock and demo systems, and MUST be labeled as such in the README.

### 3.2 TypeScript frontends

Frontends use strict TypeScript (`"strict": true`) and the framework's idiomatic project
layout. Shared UI state stays in the framework's primitives; no ad-hoc global stores.
Formatting and linting run in CI, not just on laptops.

### 3.3 Shared library first

Cross-service concerns — error shapes, pagination, auth context, logging — MUST come from
`banking-commons`. Re-implementing a commons capability inside a service is a defect, not a
preference. If commons lacks something you need, extend commons first, then consume it.

### 3.4 Logging

Logs are structured, one event per line, with a correlation id. Member PII (full account
numbers, government ids) MUST NOT appear in logs at any level. `System.out` and
`console.log` are not logging.

## 4. API guidelines

### 4.1 Resource naming

Resources are plural nouns (`/accounts`, `/transfers`); path parameters are opaque ids.
Verbs belong in HTTP methods, not paths. Query parameters are camelCase.

### 4.2 Versioning

Breaking changes require a new major version negotiated through `api-contracts` review.
Additive changes (new optional fields, new endpoints) are non-breaking and preferred.

### 4.3 Pagination

List endpoints MUST paginate with `page` / `pageSize` query parameters and return a
`PageMeta` object (`page`, `pageSize`, `totalItems`). Unbounded lists are a defect.

### 4.4 Idempotency for money movement

Every money-movement endpoint (transfers, bill pay) MUST accept an `Idempotency-Key` header
and replay the original result for a repeated key. A retried request must never move money
twice. Keys are UUIDs, scoped per member, retained for at least 24 hours.

### 4.5 Notifications go through notifications-service

Member-facing notifications (email, push, in-app alerts) MUST be sent via
`notifications-service`. Services and batch jobs never send member communication directly —
one service owns templates, delivery, and the send log, so communication stays auditable.
