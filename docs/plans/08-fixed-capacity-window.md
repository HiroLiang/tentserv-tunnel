# Plan 08: Fixed-Capacity Window

Status: Draft for discussion; implementation readiness and completion are not yet asserted.
Estimated engineering share: 8% of the root plan's total.
Dependencies: [Plan 07: Reliable Stream](07-reliable-stream.md)

[Root project plan](project-plan.md) · [Planning index](README.md) · [Project instructions](../../AGENTS.md)

## Goal

Provide an opaque fixed-capacity retained range that advances as producers append new encrypted records.

## Scope and implementation tasks

1. Resolve window capacity accounting, allocation reservation, writer/reader cardinality, and close/delete/expiry semantics before implementing them.
2. Implement window-instance identity, append deduplication, monotonic record positions, and earliest/latest retained positions.
3. Atomically coordinate append publication, whole-record eviction, metadata updates, and quota accounting.
4. Allow authorized decryption from retained boundaries after earlier records are gone; avoid key/nonce reuse when storage slots are reused.
5. Provide follow/read operations and explicit evicted-cursor/gap responses without silently claiming lossless resumption.
6. Expose API/CLI operations and bound temporary staging, cleanup lag, and slow-reader resource use.

## Acceptance checks

- [ ] P08-C01: Continuous append beyond capacity leaves retained storage within the selected accounting limit.
- [ ] P08-C02: Oversized records follow an explicit rejection/fragmentation contract.
- [ ] P08-C03: Eviction preserves integrity and decryptability of remaining records.
- [ ] P08-C04: Readers behind the retained range receive the correct explicit gap and available positions.
- [ ] P08-C05: Retrying an append does not advance the window or evict records twice.
- [ ] P08-C06: Crash/restart and concurrent read/eviction preserve consistent range and owner accounting.
- [ ] P08-C07: A slow reader or missing receipt does not create an unbounded retention backlog.

## Deliverables

- Generic window API/CLI and retained-range metadata.
- Eviction-safe encryption and accounting integration.
- Capacity, gap, concurrency, and restart checks.

## Decisions for the plan discussion

- Full capacity reservation versus occupancy charging, and exact overhead accounting.
- Initial writer/reader count, reader starting positions, and authorization/key distribution.
- Whether close preserves the final range; deletion, expiry, and resizing support.

## Boundaries and dependencies

Window capacity does not define elapsed time or payload meaning. Application-specific interpretation and lossless recovery after eviction are outside its contract.

All prerequisite plans must provide the interfaces and verified guarantees used here. Changes to their contracts must update the affected plans and root requirement ownership.

## Evidence and completion

Implementation evidence has not been collected for this plan. When work is performed, record the relevant revision, configuration, checks executed, outcomes, and unresolved failures. Mark the plan complete only after its required checks and the root completion rules are satisfied.

The estimate includes this plan's implementation, relevant tests, integration, and technical documentation. It is not a measured completion percentage.

## Git checkpoint

Mark completion after capacity and gap checks pass under the defined crash/concurrency cases. Plan 09 may precede this plan once Plan 07 is complete.

Follow the root plan's Git procedure and the repository's authorization rules; this document does not execute Git operations.
