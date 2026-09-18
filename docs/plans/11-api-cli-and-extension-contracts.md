# Plan 11: API, CLI, and Extension Contracts

Status: Draft for discussion; implementation readiness and completion are not yet asserted.
Estimated engineering share: 3% of the root plan's total.
Dependencies: [Plan 10: Object Storage Backend](10-object-storage-backend.md)

[Root project plan](project-plan.md) · [Planning index](README.md) · [Project instructions](../../AGENTS.md)

## Goal

Make the already implemented interfaces consistent, complete, and suitable for supported extension providers.

## Scope and implementation tasks

1. Audit API and CLI coverage for enrollment, pairing, channels, text, chunk, stream, window, forwarding, status, and cancellation.
2. Unify error/status meanings, configuration precedence, machine-readable output, exit behavior, and version handling.
3. Finalize authentication-provider and client-side invitation-handoff extension contracts with explicit trust and secret-handling boundaries.
4. Ensure sensitive input/output behavior and diagnostics follow the selected confidentiality policy.
5. Provide API references and operational documentation; place feature-usage examples only in the repository root README.md.
6. Check that interfaces reuse the same client/domain behavior instead of implementing conflicting semantics.

## Acceptance checks

- [ ] P11-C01: Every committed capability has its intended complete API and CLI operation path.
- [ ] P11-C02: Equivalent API and CLI operations enforce the same authorization, error, and lifecycle rules.
- [ ] P11-C03: Provider rejection, timeout, and malformed responses fail within the defined boundary.
- [ ] P11-C04: Logs, help output, and error paths do not leak credentials, private keys, or pairing secrets.
- [ ] P11-C05: Supported version mismatches and configuration errors produce actionable failures.
- [ ] P11-C06: Development documents contain generic contracts rather than application-specific narratives.

## Deliverables

- Consistent public API/CLI behavior and extension contracts.
- Reference documentation and root-README usage guidance.
- Coverage and sensitive-output verification.

## Decisions for the plan discussion

- Final public naming, error stability, and compatibility policy.
- Initial provider/handoff adapter packaging and supported extension surface.
- Required reference and CLI output formats.

## Boundaries and dependencies

API and CLI functionality is built in every feature plan. This 3% allocation is for consolidation and completeness, not delayed implementation of the entire client interface.

All prerequisite plans must provide the interfaces and verified guarantees used here. Changes to their contracts must update the affected plans and root requirement ownership.

## Evidence and completion

Implementation evidence has not been collected for this plan. When work is performed, record the relevant revision, configuration, checks executed, outcomes, and unresolved failures. Mark the plan complete only after its required checks and the root completion rules are satisfied.

The estimate includes this plan's implementation, relevant tests, integration, and technical documentation. It is not a measured completion percentage.

## Git checkpoint

Commit interface/reference consistency changes with compatibility checks; prepare a release-candidate checkpoint for Plan 12.

Follow the root plan's Git procedure and the repository's authorization rules; this document does not execute Git operations.
