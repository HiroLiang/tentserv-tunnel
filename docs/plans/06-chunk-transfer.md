# Plan 06: Chunk Transfer

Status: Draft for discussion; implementation readiness and completion are not yet asserted.
Estimated engineering share: 9% of the root plan's total.
Dependencies: [Plan 05: Endpoint and Key Lifecycle](05-endpoint-and-key-lifecycle.md)

[Root project plan](project-plan.md) · [Planning index](README.md) · [Project instructions](../../AGENTS.md)

## Goal

Transfer bounded objects as authenticated resumable parts with reliable cleanup and owner accounting.

## Scope and implementation tasks

1. Define transfer and chunk identities, part boundaries, completion information, and authenticated integrity metadata.
2. Implement incremental upload, retrieval, progress queries, durable part receipts, and safe retransmission.
3. Release acknowledged parts and permit larger transfers to advance through a bounded quota window.
4. Persist sender and receiver progress for supported process-restart recovery.
5. Implement whole-transfer cancellation, expiration, late-write rejection, and orphan cleanup.
6. Expose the complete feature through the existing API and CLI, and extend both metadata drivers.

## Acceptance checks

- [ ] P06-C01: Mid-transfer disconnection or process restart resumes to identical completed content.
- [ ] P06-C02: Duplicate or reordered parts follow the selected rules without duplicate delivery or accounting.
- [ ] P06-C03: A transfer larger than the staging quota makes progress when the recipient consumes acknowledged parts.
- [ ] P06-C04: Tampered parts and inconsistent completion metadata are rejected.
- [ ] P06-C05: Cancellation racing retries prevents data resurrection and releases space once.
- [ ] P06-C06: Oversized parts or exhausted capacity produce explicit bounded behavior.

## Deliverables

- API/CLI chunk operations and durable transfer progress.
- Updated persistence operations and cleanup.
- Integrity, retry, restart, and cancellation checks.

## Decisions for the plan discussion

- Part-size limits and initial sequential/parallel/out-of-order support.
- Completion-integrity format and initial random-access scope.
- Resume granularity and transfer retention defaults.

## Boundaries and dependencies

Payload interpretation is outside this plan. Reuse established receipt, identity, authorization, and accounting machinery.

All prerequisite plans must provide the interfaces and verified guarantees used here. Changes to their contracts must update the affected plans and root requirement ownership.

## Evidence and completion

Implementation evidence has not been collected for this plan. When work is performed, record the relevant revision, configuration, checks executed, outcomes, and unresolved failures. Mark the plan complete only after its required checks and the root completion rules are satisfied.

The estimate includes this plan's implementation, relevant tests, integration, and technical documentation. It is not a measured completion percentage.

## Git checkpoint

Commit the initial completed-transfer path, then recovery/cancellation increments with their checks; mark completion only when the full transfer acceptance set passes.

Follow the root plan's Git procedure and the repository's authorization rules; this document does not execute Git operations.
