# Plan 04: Storage Backends and Consistency

Status: Draft for discussion; implementation readiness and completion are not yet asserted.
Estimated engineering share: 9% of the root plan's total.
Dependencies: [Plan 03: Accounts, Authentication, and Quota](03-accounts-authentication-and-quota.md)

[Root project plan](project-plan.md) · [Planning index](README.md) · [Project instructions](../../AGENTS.md)

## Goal

Provide PostgreSQL 18 and SQLite metadata backends plus local ciphertext storage with consistent externally visible behavior.

## Scope and implementation tasks

1. Implement domain persistence operations and backend-specific transactions for identities, invitations, channels, delivery records, prekeys, and quotas.
2. Complete the PostgreSQL 18 driver and bring the initial SQLite driver to the same documented contract.
3. Define and exercise schema migrations and compatibility checks for each backend.
4. Implement local content storage with generated storage keys and safe ownership/index binding.
5. Coordinate quota reservation, payload staging, metadata commit, deletion, and orphan reconciliation without assuming a cross-store transaction.
6. Define the content-store interface that Plan 10 will implement for object storage; add operations for later features when their contracts are resolved.

## Acceptance checks

- [ ] P04-C01: Both metadata drivers pass the same domain contract and concurrent quota/prekey tests.
- [ ] P04-C02: Interruption between payload write and metadata commit leaves a recoverable state.
- [ ] P04-C03: Dangling indexes and orphan payloads are detected and handled without exposing another owner's data.
- [ ] P04-C04: Repeated deletion or reconciliation does not corrupt quota accounting.
- [ ] P04-C05: Migrations preserve the supported existing state and reject unsupported versions clearly.
- [ ] P04-C06: The local content backend prevents unauthorized path selection and enforces its storage-root boundary.

## Deliverables

- PostgreSQL 18 and SQLite parity for implemented domain operations.
- A local content backend and migration/reconciliation machinery.
- Shared persistence and content-store conformance checks.

## Decisions for the plan discussion

- Migration tooling and downgrade/restore policy.
- Local storage layout and small-message placement.
- Single-instance versus any required multi-instance guarantees.
- Physical staging overhead and the declared durability boundary.

## Boundaries and dependencies

Bulk object storage is implemented in Plan 10. This plan establishes its contract and the initial local implementation without prebuilding unneeded backend abstractions.

All prerequisite plans must provide the interfaces and verified guarantees used here. Changes to their contracts must update the affected plans and root requirement ownership.

## Evidence and completion

Implementation evidence has not been collected for this plan. When work is performed, record the relevant revision, configuration, checks executed, outcomes, and unresolved failures. Mark the plan complete only after its required checks and the root completion rules are satisfied.

The estimate includes this plan's implementation, relevant tests, integration, and technical documentation. It is not a measured completion percentage.

## Git checkpoint

Commit each migration with its corresponding code and verification. Preserve a reviewed recovery point before applying a state-changing migration to persistent environments.

Follow the root plan's Git procedure and the repository's authorization rules; this document does not execute Git operations.
