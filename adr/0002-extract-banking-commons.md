# ADR-0002: Extract banking-commons as a shared library

- **Status:** Accepted
- **Date:** 2026-03-02
- **Deciders:** Aisha Khan, Marcus Bell, Devon Okafor
- **Handbook clauses:** §3.3, §4.6

## Context

Accounts, payments, and notifications each carried their own copies of error responses,
pagination types, auth-context plumbing, and logging helpers. The copies had already
drifted: three different error shapes, two pagination conventions. §3.3 (shared library
first) had no library to point at.

## Decision

Extract the shared concerns into a `banking-commons` Java library: error model, auth-context
stub, logging utilities, pagination types. Services consume it as a versioned Maven
dependency resolved through JitPack from tagged releases. Rollout: extract from
accounts-service (the least-drifted copy), then migrate payments and notifications;
statement-batch migrates when it next takes feature work.

## Consequences

A commons change now needs a tag + version bump in every consumer — deliberate friction that
keeps the library small. In exchange, §3.3 is enforceable, and the §4.6 error envelope has a
single reference implementation to land in.
