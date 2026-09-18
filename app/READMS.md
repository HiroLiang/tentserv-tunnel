# User-Side Architecture and Agent Guide

[Project instructions](../AGENTS.md) · [Server guide](../server/READMS.md) · [Planning index](../docs/plans/README.md) · [Root plan](../docs/plans/project-plan.md)

## Role and reading order

`app/` is the base directory for the complete user-side endpoint toolchain. It owns the user-facing API/CLI surfaces, endpoint identity and private state, peer authentication, payload encryption/decryption, transfer recovery, and local TCP forwarding adapters. The server relays and stores ciphertext; endpoint code owns the plaintext boundary.

Read AGENTS.md first, then this guide, the relevant plan, and the nearest implementation. The selected API/CLI scope does not settle whether the programming interface runs in-process or through a local service. Process boundaries and supported platforms remain part of Plan 00.

## Inventory and implementation status

Inventory date: 2026-09-18. The local workspace contains a draft Rust workspace manifest named `app/ Cargo.toml`, with a leading space before `Cargo.toml`, and an empty `app/creates/` directory. There are no implemented workspace crates or established Rust source conventions to copy yet.

The manifest lists members under `crates/`, but that directory and its members do not currently exist. Do not assume normal Cargo workspace discovery or a runnable client. Resolve the manifest filename and `creates/` versus `crates/` discrepancy during an authorized scaffold change; this documentation task preserves them.

This documentation baseline intentionally excludes the manifest and source/build assets from its Git commit. Paths below describe local draft intent and may not exist in a documentation-only checkout. Draft names do not settle final module boundaries, language choices, or additional product scope.

## Draft workspace map

The following paths are relative to `app/`. They are listed in the draft manifest but are not implemented. Responsibilities are working interpretations of those names to confirm in [Plan 00](../docs/plans/00-core-contracts-and-technology.md).

| Draft path | Candidate responsibility | Decision boundary |
| --- | --- | --- |
| `crates/tentserv-cli` | Parse user commands and present results from the client core. | Exact command and error contracts remain open. |
| `crates/tentserv-daemon` | Host long-running endpoint work or a local API, if selected. | A mandatory daemon and its IPC boundary are not decided. |
| `crates/tunnel-core` | Coordinate endpoint/channel state and text/chunk/stream/window lifecycles. | Public API and module boundaries remain open. |
| `crates/tunnel-crypto` | Own cryptographic operations, peer verification, and secret-state boundaries. | Exact reviewed libraries and composition remain open. |
| `crates/tunnel-transport` | Communicate with the relay and manage connection/reconnection mechanics. | Wire transport and versioning remain open. |
| `crates/local-proxy` | Adapt explicitly authorized local sockets to secure bidirectional transfer. | Listener, target, and connection-lifecycle contracts remain open. |
| `crates/config-store` | Store local configuration and, if selected, durable endpoint state. | Secret storage and transactional state ownership require explicit design. |
| `crates/service-manager` | Potential operating-system service integration. | Platforms and whether this component is needed remain open. |
| `crates/updater` | Potential software-update integration. | A manifest entry does not approve an automatic-update feature or implementation plan. |

## Code organization and style guidance

No Rust implementation style is established yet. Do not describe these guidelines as observed code or silently scaffold all listed crates.

- Select public APIs, error conventions, formatting/linting, asynchronous runtime, and crate boundaries in Plan 00 before implementation.
- Keep command parsing and presentation in the CLI layer. Put reusable feature behavior behind the agreed client API so API and CLI callers use the same lifecycle rules.
- Keep transport connection handling separate from peer authentication and cryptographic state. A successful server login does not authenticate the remote endpoint's key.
- Define one explicit owner for durable encryption progress, outbox/inbox state, deduplication, and receipts. Avoid competing state copies in CLI, daemon, and transport code.
- Keep cryptographic operations behind a narrow boundary using the selected reviewed libraries. Local payload encryption must precede relay upload; authenticated verification and durable receipt precede storage-release acknowledgment.
- Propagate backpressure to producers and expose explicit cancellation, expiry, terminal errors, and window gaps. Do not hide these states behind an unbounded local queue.
- Keep TCP socket lifecycle in its adapter. Resuming native transfer records does not authorize replaying bytes into a replacement application socket.
- Keep secrets out of ordinary logs and error output. Define secret-bearing CLI input/output and local persistence deliberately in the owning plan.

## Finding a feature

The locations below are draft lookup targets, not existing implementations. Once a plan assigns concrete paths, replace the candidate references with the actual modules.

| Work area | Candidate modules or boundary | Owning plans |
| --- | --- | --- |
| API shape, CLI/runtime split, platforms | `tentserv-cli`, `tentserv-daemon`, `tunnel-core` | [00](../docs/plans/00-core-contracts-and-technology.md) |
| Enrollment and server selection | CLI/core/transport plus local configuration | [01](../docs/plans/01-e2ee-text-baseline.md), [03](../docs/plans/03-accounts-authentication-and-quota.md) |
| PAKE pairing and authenticated text | `tunnel-core`, `tunnel-crypto`, `tunnel-transport` | [01](../docs/plans/01-e2ee-text-baseline.md) |
| Outbox/inbox, receipts, retry, restart recovery | Core plus the agreed durable-state owner | [02](../docs/plans/02-delivery-durability-and-recovery.md) |
| Identity, key continuity, rotation, revocation | `tunnel-crypto`, core, and protected local persistence | [05](../docs/plans/05-endpoint-and-key-lifecycle.md) |
| Resumable chunk transfer | Core transfer state, encrypted records, persistence, API/CLI | [06](../docs/plans/06-chunk-transfer.md) |
| Reliable stream, credits, EOF, reconnection | Core/transport, encrypted records, durable cursors, API/CLI | [07](../docs/plans/07-reliable-stream.md) |
| Window append/read, retained boundaries, gap handling | Core, independently readable encrypted records, API/CLI | [08](../docs/plans/08-fixed-capacity-window.md) |
| TCP listeners, destinations, half-close/reset | `local-proxy` using shared authorization and stream contracts | [09](../docs/plans/09-tcp-forwarding.md) |
| Complete interfaces and invitation-handoff adapters | Client API/CLI and a separately defined trusted handoff boundary | [11](../docs/plans/11-api-cli-and-extension-contracts.md) |
| Packaging and supported process lifecycle | CLI/runtime packaging; service integration only if agreed | [12](../docs/plans/12-deployment-and-product-acceptance.md) |

## Cross-component changes

Coordinate message formats, identifiers, state transitions, errors, quota signals, and compatibility behavior with the server guide and owning plan. Share an agreed protocol contract without assuming that Go and Rust can share a source module. Keep database-driver and ciphertext-store implementation inside the server boundary; keep endpoint secrets and client-side recovery state inside the endpoint boundary.

The server's account-authentication provider and the client's pairing-secret handoff adapter have different trust roles. Do not combine them into one extension that lets the relay choose or bypass peer verification.

## Keeping this guide useful

When an authorized change establishes a crate, record its real path, public entry points, adjacent tests, coding conventions, and owning plan here. Remove speculative entries when rejected rather than leaving them as implied requirements. Record acceptance evidence in the relevant plan and keep application-specific examples in the root README.md.
