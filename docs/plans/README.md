# Implementation Plans

This directory holds plans for implementing the project's currently agreed requirements. The [root project plan](project-plan.md) is the authoritative entry point for the implementation roadmap, estimated effort allocation, dependencies, product acceptance, and Git checkpoints.

## Document relationships

- [AGENTS.md](../../AGENTS.md): project interaction rules, confirmed requirements, and design decisions.
- [Root project plan](project-plan.md): product scope, child-plan index, effort estimates, milestone gates, and version-control timing.
- Child plans linked from the root: scoped tasks, acceptance checks, deliverables, dependencies, and decisions for individual discussion.

Plans describe generic components, interfaces, protocol behavior, persistence, and verification. Application use cases and feature-usage examples belong only in the repository's root README.md.

## Current status

The roadmap structure and creation of its documents have been approved. The root plan and Plans 00–12 now exist. Each child is a draft for sequential discussion; creating the documents does not claim implementation readiness or completion.

Begin with [Plan 00: Core Contracts and Technology](00-core-contracts-and-technology.md). The first usable product milestone is [Plan 01: E2EE Text Baseline](01-e2ee-text-baseline.md).

## Planning workflow

1. Use the root plan to check scope, order, dependencies, and effort allocation.
2. Discuss one child plan and resolve its blocking decisions before marking it ready.
3. Implement within the project interaction rules and collect its acceptance evidence.
4. Update the child status and root milestone state when its completion conditions pass.
5. Record Git checkpoints according to the root procedure when the corresponding actions are authorized.

All plan documents must be written in English. The existence of a plan does not override AGENTS.md. Effort percentages are estimates of engineering work, not measured implementation progress.
