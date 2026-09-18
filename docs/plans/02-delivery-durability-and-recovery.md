# Plan 02: Delivery Durability and Recovery

Status: Draft for discussion; implementation readiness and completion are not yet asserted.
Estimated engineering share: 11% of the root plan's total.
Dependencies: [Plan 01: E2EE Text Baseline](01-e2ee-text-baseline.md)

[Root project plan](project-plan.md) · [Planning index](README.md) · [Project instructions](../../AGENTS.md)

## Goal

Make accepted delivery state recoverable across retries, lost acknowledgments, and process interruption.

## Scope and implementation tasks

1. Persist the client outbox, inbox, deduplication information, and cryptographic progress with explicit crash-consistency rules.
2. Define relay acceptance and endpoint durable-receipt records, including authenticated end-to-end receipts where peer receipt is claimed.
3. Retry committed ciphertext using stable identities. Prevent an uncertain result from causing a new logical send or new encryption under a reused key/nonce.
4. Implement idempotent acknowledgment, cancellation, expiration, terminal-state retention, and quota release.
5. Reconcile pending work on startup and reconnect; preserve valid progress and report unrecoverable local state explicitly.
6. Introduce reusable fault-injection points at persistence, transmission, acknowledgment, and cleanup boundaries.

## Acceptance checks

- [ ] P02-C01: A dropped acknowledgment causes a safe retry without duplicate transport delivery or duplicate capacity release.
- [ ] P02-C02: Process interruption before and after each durable boundary produces the documented recovery state.
- [ ] P02-C03: Two different plaintext records never use the same key/nonce as a result of retry or recoverable restart.
- [ ] P02-C04: A forged relay-only receipt cannot masquerade as authenticated durable receipt by the peer.
- [ ] P02-C05: Cancellation racing a delayed upload cannot resurrect readable data.
- [ ] P02-C06: Externally visible delivery milestones distinguish relay acceptance from endpoint receipt and application completion.

## Deliverables

- A reusable delivery and recovery state machine.
- Persistent client-state handling and a failure-boundary test suite.
- Documented receipt and cancellation semantics.

## Decisions for the plan discussion

- Client persistence and secret-protection mechanisms.
- Terminal-record retention and reconnect retry policy.
- Supported response to snapshot rollback, cloned state, or unrecoverable local corruption.

## Boundaries and dependencies

Transport deduplication does not guarantee exactly-once external application effects. Later plans reuse this state machinery and extend its checks for their own semantics.

All prerequisite plans must provide the interfaces and verified guarantees used here. Changes to their contracts must update the affected plans and root requirement ownership.

## Evidence and completion

Implementation evidence has not been collected for this plan. When work is performed, record the relevant revision, configuration, checks executed, outcomes, and unresolved failures. Mark the plan complete only after its required checks and the root completion rules are satisfied.

The estimate includes this plan's implementation, relevant tests, integration, and technical documentation. It is not a measured completion percentage.

## Git checkpoint

Commit each verified recovery invariant with its checks. Complete this plan only after its fault-boundary matrix passes.

Follow the root plan's Git procedure and the repository's authorization rules; this document does not execute Git operations.
