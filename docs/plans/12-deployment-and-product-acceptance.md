# Plan 12: Deployment and Product Acceptance

Status: Draft for discussion; implementation readiness and completion are not yet asserted.
Estimated engineering share: 4% of the root plan's total.
Dependencies: [Plan 11: API, CLI, and Extension Contracts](11-api-cli-and-extension-contracts.md)

[Root project plan](project-plan.md) · [Planning index](README.md) · [Project instructions](../../AGENTS.md)

## Goal

Verify and package the complete agreed product across its supported cloud and self-hosted profiles.

## Scope and implementation tasks

1. Prepare reproducible installation/configuration, startup/shutdown, data-path, migration, and cleanup procedures for supported profiles.
2. Verify the selected metadata/content-store combinations and the declared restart, upgrade, and recovery guarantees.
3. Run the root acceptance matrix across all capabilities, including negative authorization, malicious-relay, quota, resource-bound, and fault cases.
4. Validate the operating envelope defined in Plan 00 and document supported limits and failure behavior.
5. Review release contents and diagnostics for accidental inclusion of secrets or runtime data; reconcile requirements, plans, and actual behavior.
6. Record verification evidence, remaining non-blocking limitations, and the release decision. Keep unfulfilled required behavior open rather than declaring completion.

## Acceptance checks

- [ ] P12-C01: A clean supported environment can run the chosen cloud or self-hosted profile from the documented procedure.
- [ ] P12-C02: Both verification options and the self-hosted enrollment profile behave as specified.
- [ ] P12-C03: All four data primitives and TCP forwarding pass their complete workflows and failure checks.
- [ ] P12-C04: Supported restarts/upgrades preserve valid state without crossing owner boundaries or misreporting acceptance.
- [ ] P12-C05: Resource and cleanup behavior meets the declared operating envelope.
- [ ] P12-C06: Every root requirement maps to passing evidence or an explicitly agreed scope change; no required item remains unresolved.

## Deliverables

- A deployable product for the supported profiles.
- A completed requirements-to-evidence matrix and release checklist.
- A reproducible accepted release checkpoint.

## Decisions for the plan discussion

- Release packaging and supported deployment/version matrix.
- Acceptance environment, workload thresholds, and recovery targets not already fixed.
- Release identifier and final operational support boundaries.

## Boundaries and dependencies

Feature-level tests and security checks occur throughout the roadmap. This 4% allocation covers integrated acceptance and packaging; it does not absorb postponed feature work.

All prerequisite plans must provide the interfaces and verified guarantees used here. Changes to their contracts must update the affected plans and root requirement ownership.

## Evidence and completion

Implementation evidence has not been collected for this plan. When work is performed, record the relevant revision, configuration, checks executed, outcomes, and unresolved failures. Mark the plan complete only after its required checks and the root completion rules are satisfied.

The estimate includes this plan's implementation, relevant tests, integration, and technical documentation. It is not a measured completion percentage.

## Git checkpoint

Record milestone M4 and a release tag only after the full product acceptance gate passes and the corresponding Git actions are authorized.

Follow the root plan's Git procedure and the repository's authorization rules; this document does not execute Git operations.
