# Plan 00: Core Contracts and Technology

Status: Draft for discussion; implementation readiness and completion are not yet asserted.
Estimated engineering share: 5% of the root plan's total.
Dependencies: None. This plan establishes the baseline.

[Root project plan](project-plan.md) · [Planning index](README.md) · [Project instructions](../../AGENTS.md)

## Goal

Make the agreed scope precise enough to implement and verify without silently choosing unresolved behavior.

## Scope and implementation tasks

1. Review the existing implementation only within the authorized scope and identify reusable code, gaps, and constraints. Select implementation languages, supported platforms, the client API form, runtime boundaries, and the client-to-relay transport.
2. Select maintained cryptographic libraries and a reviewed composition for PAKE, authenticated peer identity, asynchronous session establishment, message encryption, and independently recoverable encrypted records. Define version negotiation and downgrade protection.
3. Define owner, endpoint, invitation, channel, transfer, record, receipt, quota reservation, and window identifiers and their state transitions.
4. Specify text atomicity, chunk completion, reliable-stream ordering/EOF/resumption, window capacity/eviction/cursors, and TCP connection lifecycle separately.
5. Define the malicious-relay threat model, plaintext boundaries, metadata exposure, peer-authenticated receipts, and explicit durability guarantees.
6. Resolve immediate implementation blockers; assign later choices to their owning plans. Establish a requirements-to-acceptance matrix and revise the effort estimates after the baseline inventory.

## Acceptance checks

- [ ] P00-C01: Every confirmed requirement in AGENTS.md has an owning plan and an observable acceptance criterion.
- [ ] P00-C02: Accepted, durably received, completed, cancelled, expired, and evicted have distinct definitions and state transitions.
- [ ] P00-C03: Cryptographic choices cover peer substitution, replay, key/nonce uniqueness, offline receipt, restart recovery, and window entry after eviction; known limitations are explicit.
- [ ] P00-C04: Each deployment profile has a documented owner model, supported storage combination, and failure-durability target.
- [ ] P00-C05: No application-specific payload interpretation enters the protocol or server contracts.

## Deliverables

- An agreed generic contract and architecture baseline.
- A traceable acceptance matrix and updated estimates.
- Decisions required to begin Plan 01, with later decisions assigned to their plans.

## Decisions for the plan discussion

- Implementation languages, target platforms, SDK versus local-service API, and initial transport.
- Exact PAKE/session/record libraries and supported security properties, including whether post-quantum support is required.
- Initial one-to-one scope, window writer/reader cardinality, baseline retention/size limits, and instance topology.
- Performance targets and the failures an accepted receipt must survive.

## Boundaries and dependencies

No feature implementation is required to complete this plan. Creating these planning documents does not settle the choices listed above.

All prerequisite plans must provide the interfaces and verified guarantees used here. Changes to their contracts must update the affected plans and root requirement ownership.

## Evidence and completion

Implementation evidence has not been collected for this plan. When work is performed, record the relevant revision, configuration, checks executed, outcomes, and unresolved failures. Mark the plan complete only after its required checks and the root completion rules are satisfied.

The estimate includes this plan's implementation, relevant tests, integration, and technical documentation. It is not a measured completion percentage.

## Git checkpoint

G0 establishes the repository baseline before this plan starts. Commit the reviewed contract decisions before starting Plan 01.

Follow the root plan's Git procedure and the repository's authorization rules; this document does not execute Git operations.
