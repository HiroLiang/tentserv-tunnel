# Plan 05: Endpoint and Key Lifecycle

Status: Draft for discussion; implementation readiness and completion are not yet asserted.
Estimated engineering share: 8% of the root plan's total.
Dependencies: [Plan 04: Storage Backends and Consistency](04-storage-backends-and-consistency.md)

[Root project plan](project-plan.md) · [Planning index](README.md) · [Project instructions](../../AGENTS.md)

## Goal

Complete endpoint trust, prekey/session maintenance, revocation, and recovery behavior around the initial secure channel.

## Scope and implementation tasks

1. Implement prekey replenishment, atomic allocation, signed-prekey rotation, and retention compatible with permitted delayed delivery.
2. Persist verified peer identities and enforce the policy for unexpected changes and authorized rotations.
3. Implement endpoint addition/removal, channel closure, credential revocation, and restrictions on future operations.
4. Specify key loss, account recovery, endpoint replacement, and supported backup/restore behavior without silently replacing peer trust.
5. Bound retained session and skipped-message state while preserving the agreed offline-delivery range.
6. Review the complete cryptographic composition and lifecycle against the active-malicious-relay threat model.

## Acceptance checks

- [ ] P05-C01: Concurrent prekey requests do not receive the same one-time allocation through the cooperative service.
- [ ] P05-C02: Valid delayed messages remain readable across supported rotations and retention intervals.
- [ ] P05-C03: An unexpected peer identity change cannot silently continue an authenticated channel.
- [ ] P05-C04: Revoked credentials cannot authorize new operations, and future key access follows the selected revocation policy.
- [ ] P05-C05: Account recovery alone does not recover old E2EE secrets or overwrite a pinned peer identity.
- [ ] P05-C06: Key/session cleanup and documented rollback handling do not reuse encryption state.

## Deliverables

- Endpoint, channel, and key lifecycle operations.
- A reviewed trust/recovery policy with explicit limits.
- Lifecycle and malicious-relay checks.

## Decisions for the plan discussion

- Rotation intervals, offline retention, and bounded skipped-key state.
- Initial recovery/backup scope and supported endpoint migration behavior.
- What revocation does to already queued data and already established sessions.

## Boundaries and dependencies

Authenticated identity continuity is required from Plan 01. This plan completes lifecycle coverage and does not postpone the baseline pairing protections.

All prerequisite plans must provide the interfaces and verified guarantees used here. Changes to their contracts must update the affected plans and root requirement ownership.

## Evidence and completion

Implementation evidence has not been collected for this plan. When work is performed, record the relevant revision, configuration, checks executed, outcomes, and unresolved failures. Mark the plan complete only after its required checks and the root completion rules are satisfied.

The estimate includes this plan's implementation, relevant tests, integration, and technical documentation. It is not a measured completion percentage.

## Git checkpoint

Record milestone M2 after Plans 02–05 pass: durable text with complete account, storage, quota, and key foundations.

Follow the root plan's Git procedure and the repository's authorization rules; this document does not execute Git operations.
