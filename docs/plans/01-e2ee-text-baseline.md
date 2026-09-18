# Plan 01: E2EE Text Baseline

Status: Draft for discussion; implementation readiness and completion are not yet asserted.
Estimated engineering share: 12% of the root plan's total.
Dependencies: [Plan 00: Core Contracts and Technology](00-core-contracts-and-technology.md)

[Root project plan](project-plan.md) · [Planning index](README.md) · [Project instructions](../../AGENTS.md)

## Goal

Deliver the first usable end-to-end path: two endpoints pair, send encrypted text through the relay, and receive it later.

## Scope and implementation tasks

1. Build the minimal server, shared client core, API entry points, and CLI using the contracts selected in Plan 00.
2. Start with a local self-hosted SQLite profile, administrator-assigned quota ownership, minimal durable records, and explicit endpoint/channel authorization.
3. Generate and store endpoint secrets locally. Implement the selected PAKE exchange, key confirmation, peer-identity binding, and manual secret handoff without exposing the secret to the relay.
4. Implement the selected secure session and text envelope, including stable message IDs and an authenticated peer relationship.
5. Implement complete-message acceptance or rejection, bounded storage, delayed retrieval, receipt status, and basic receipt-driven cleanup.
6. Expose pairing, text send/receive, and status through the API and CLI. Keep the minimal interfaces compatible with later authentication and persistence backends.

## Acceptance checks

- [ ] P01-C01: Two fresh endpoints can pair and exchange text using both the API and CLI.
- [ ] P01-C02: A sender can submit while the recipient is offline; the recipient later obtains and decrypts the original message.
- [ ] P01-C03: An unauthorized endpoint cannot read, write, or delete another channel's data.
- [ ] P01-C04: Modified ciphertext and substituted peer identity material fail verification.
- [ ] P01-C05: Insufficient capacity rejects a whole message without exposing a partial accepted message.
- [ ] P01-C06: Relay storage and operational output do not contain endpoint private keys, pairing secrets, or test payload plaintext.

## Deliverables

- A demonstrable E2EE text path with delayed receipt.
- Minimal API and CLI operations and their automated checks.
- A documented local startup procedure under the selected runtime.

## Decisions for the plan discussion

- Initial text-size and invitation-lifetime defaults.
- Minimal CLI operation names and the selected API's return/status model.
- Where small ciphertext messages live in the initial SQLite profile.

## Boundaries and dependencies

This is the first usable vertical slice. Full crash-boundary verification, cloud account verification, PostgreSQL parity, and key lifecycle coverage are completed by subsequent plans; their interfaces are established here.

All prerequisite plans must provide the interfaces and verified guarantees used here. Changes to their contracts must update the affected plans and root requirement ownership.

## Evidence and completion

Implementation evidence has not been collected for this plan. When work is performed, record the relevant revision, configuration, checks executed, outcomes, and unresolved failures. Mark the plan complete only after its required checks and the root completion rules are satisfied.

The estimate includes this plan's implementation, relevant tests, integration, and technical documentation. It is not a measured completion percentage.

## Git checkpoint

Commit coherent implementation and verification changes as they pass. At completion, record milestone M1: the E2EE text baseline.

Follow the root plan's Git procedure and the repository's authorization rules; this document does not execute Git operations.
