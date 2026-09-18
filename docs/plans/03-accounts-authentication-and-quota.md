# Plan 03: Accounts, Authentication, and Quota

Status: Draft for discussion; implementation readiness and completion are not yet asserted.
Estimated engineering share: 10% of the root plan's total.
Dependencies: [Plan 02: Delivery Durability and Recovery](02-delivery-durability-and-recovery.md)

[Root project plan](project-plan.md) · [Planning index](README.md) · [Project instructions](../../AGENTS.md)

## Goal

Bind independent endpoints to stable quota owners and support the agreed cloud and self-hosted enrollment policies.

## Scope and implementation tasks

1. Implement stable account/owner IDs, verified authentication bindings, endpoint membership, and permission-scoped credentials.
2. Implement email OTP and TOTP as alternative verification methods, including expiry, replay prevention, attempt limits, and secure first-use enrollment.
3. Define TOTP-only account creation and shared-secret provisioning; do not assume that TOTP proves ownership of an email address.
4. Provide the authentication-provider contract and a self-hosted mode without external user verification, while preserving endpoint credentials and channel authorization.
5. Aggregate usage and reservations across all endpoints and channels of the sending owner; implement atomic reservation, commitment, and release.
6. Reserve operational resources for reads, receipts, cancellation, and cleanup, and expose owner usage/status for future accounting.

## Acceptance checks

- [ ] P03-C01: Email OTP and TOTP can each complete their intended account flow; expired, replayed, or invalid proofs fail.
- [ ] P03-C02: Endpoints linked to one owner share one quota; opening another endpoint or channel does not increase available capacity.
- [ ] P03-C03: Concurrent senders cannot spend the same capacity twice.
- [ ] P03-C04: A client cannot select an unauthorized owner or charge another owner's allocation.
- [ ] P03-C05: Repeated cleanup operations release each allocation once.
- [ ] P03-C06: Self-hosted account-verification bypass does not bypass pairing or channel authorization.

## Deliverables

- Cloud verification flows and the self-hosted enrollment profile.
- Owner/endpoint authorization and account-wide quota operations.
- Authentication and concurrent-accounting checks.

## Decisions for the plan discussion

- First-use identity naming/linking and recovery, especially for TOTP-only accounts.
- Initial authentication-provider integration contract and credential lifetimes.
- Self-hosted owner provisioning and quota defaults.
- The precise stored-byte and reservation accounting convention.

## Boundaries and dependencies

Plan 01 already supplies minimal ownership and limits. This plan completes the account/provider model. Commercial pricing, payment collection, and subscription management are not part of the agreed implementation scope.

All prerequisite plans must provide the interfaces and verified guarantees used here. Changes to their contracts must update the affected plans and root requirement ownership.

## Evidence and completion

Implementation evidence has not been collected for this plan. When work is performed, record the relevant revision, configuration, checks executed, outcomes, and unresolved failures. Mark the plan complete only after its required checks and the root completion rules are satisfied.

The estimate includes this plan's implementation, relevant tests, integration, and technical documentation. It is not a measured completion percentage.

## Git checkpoint

Commit authentication and quota work in separately reviewable increments; require negative authorization and concurrency checks before completion.

Follow the root plan's Git procedure and the repository's authorization rules; this document does not execute Git operations.
