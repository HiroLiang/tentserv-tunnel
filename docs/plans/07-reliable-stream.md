# Plan 07: Reliable Stream

Status: Draft for discussion; implementation readiness and completion are not yet asserted.
Estimated engineering share: 10% of the root plan's total.
Dependencies: [Plan 06: Chunk Transfer](06-chunk-transfer.md)

[Root project plan](project-plan.md) · [Planning index](README.md) · [Project instructions](../../AGENTS.md)

## Goal

Provide ordered ongoing transfer with bounded buffering, resumable progress, and explicit completion and interruption semantics.

## Scope and implementation tasks

1. Implement stream identities, ordered records, authenticated ending, and explicit stream lifecycle states.
2. Implement enforced upload credits or the selected equivalent, including reserved in-flight data and producer backpressure.
3. Persist encryption/receive progress using the selected resumable record scheme.
4. Reconcile durable cursors after reconnect/restart; make pause/resume state queryable independently of notifications.
5. Distinguish source completion, relay acceptance, recipient receipt, cancellation, expiry, and recoverable disconnection.
6. Expose API/CLI streaming and status, and keep control operations available when data capacity is exhausted.

## Acceptance checks

- [ ] P07-C01: Sustained writes with a slower reader keep relay and local queues within their declared bounds.
- [ ] P07-C02: Lost pause/resume notifications do not permit unauthorized uploads or indefinite unobservable waiting.
- [ ] P07-C03: Accepted valid data resumes after supported endpoint/relay restart without silent gaps or duplicates.
- [ ] P07-C04: Truncation is distinguishable from authenticated EOF.
- [ ] P07-C05: Capacity release allows progress without violating per-owner reservations.
- [ ] P07-C06: Expiry/cancellation is terminal, while disconnection alone preserves valid buffered progress.

## Deliverables

- API/CLI reliable streams and durable cursors.
- Bounded flow control and reconnect/restart handling.
- Ordering, EOF, resource-bound, and fault-recovery checks.

## Decisions for the plan discussion

- Record/checkpoint format and continuation token/cursor semantics.
- Queue, credit, idle, and absolute-expiry limits.
- Source behavior when it cannot comply with backpressure.

## Boundaries and dependencies

This plan retains valid pending data for delivery. It does not adopt window eviction semantics or promise recovery of external computation.

All prerequisite plans must provide the interfaces and verified guarantees used here. Changes to their contracts must update the affected plans and root requirement ownership.

## Evidence and completion

Implementation evidence has not been collected for this plan. When work is performed, record the relevant revision, configuration, checks executed, outcomes, and unresolved failures. Mark the plan complete only after its required checks and the root completion rules are satisfied.

The estimate includes this plan's implementation, relevant tests, integration, and technical documentation. It is not a measured completion percentage.

## Git checkpoint

Record a verified reliable-stream checkpoint before implementing dependent window or TCP behavior.

Follow the root plan's Git procedure and the repository's authorization rules; this document does not execute Git operations.
