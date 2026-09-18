# Plan 09: TCP Forwarding

Status: Draft for discussion; implementation readiness and completion are not yet asserted.
Estimated engineering share: 7% of the root plan's total.
Dependencies: [Plan 07: Reliable Stream](07-reliable-stream.md)

[Root project plan](project-plan.md) · [Planning index](README.md) · [Project instructions](../../AGENTS.md)

## Goal

Forward explicitly authorized TCP connections through the authenticated encrypted transport with correct socket lifecycle behavior.

## Scope and implementation tasks

1. Implement local listeners, paired destination authorization, connection opening, and clear connection errors.
2. Assign independent logical stream identities to sockets and preserve ordered bidirectional bytes.
3. Implement bounded flow control, partial reads/writes, concurrent connections, and per-direction sending-owner accounting.
4. Map half-close, EOF, reset, cancellation, and timeout behavior explicitly.
5. Define when a temporary relay interruption can be hidden while local sockets remain alive, and when it must surface to the caller.
6. Expose API/CLI listener and forwarding lifecycle operations using the existing client core.

## Acceptance checks

- [ ] P09-C01: Bidirectional payloads arrive byte-for-byte in order.
- [ ] P09-C02: Concurrent connections do not mix data, credentials, or lifecycle events.
- [ ] P09-C03: Unauthorized destinations and listener exposure are rejected.
- [ ] P09-C04: Half-close and reset behavior are propagated correctly, including during backpressure.
- [ ] P09-C05: Old queued bytes are never attached to an unrelated replacement socket.
- [ ] P09-C06: Slow peers and connection churn remain within declared resource limits.

## Deliverables

- API/CLI TCP forwarding with explicit target authorization.
- Connection lifecycle and transport integration.
- Byte-integrity, concurrency, half-close, and failure checks.

## Decisions for the plan discussion

- Listener binding defaults, destination naming, and permissions.
- Connection/open/idle timeouts and relay-disconnection grace behavior.
- Limits on active sockets and per-connection buffering.

## Boundaries and dependencies

Transport recovery does not recreate a terminated application TCP session. UDP forwarding and other new transport primitives require separate scope decisions.

All prerequisite plans must provide the interfaces and verified guarantees used here. Changes to their contracts must update the affected plans and root requirement ownership.

## Evidence and completion

Implementation evidence has not been collected for this plan. When work is performed, record the relevant revision, configuration, checks executed, outcomes, and unresolved failures. Mark the plan complete only after its required checks and the root completion rules are satisfied.

The estimate includes this plan's implementation, relevant tests, integration, and technical documentation. It is not a measured completion percentage.

## Git checkpoint

Record milestone M3 when Plans 06–09 are complete: all four data primitives and TCP forwarding meet their feature acceptance criteria.

Follow the root plan's Git procedure and the repository's authorization rules; this document does not execute Git operations.
