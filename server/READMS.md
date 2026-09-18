# Server Architecture and Agent Guide

[Project instructions](../AGENTS.md) · [Client guide](../app/READMS.md) · [Planning index](../docs/plans/README.md) · [Root plan](../docs/plans/project-plan.md)

## Role and reading order

`server/` is the base directory for the server-side relay. Its responsibilities are enrollment policy, account authentication, endpoint/channel authorization, ciphertext routing, owner-wide quotas, and metadata/payload persistence. Endpoint private keys and payload decryption belong to the user-side toolchain.

Read AGENTS.md first, then this guide, the relevant plan, and the nearest implementation. This guide does not grant permission to edit source, run commands, or make unresolved architecture decisions.

## Inventory and implementation status

Inventory date: 2026-09-18. The local workspace contains a hand-written Go HTTP scaffold and a draft SQL migration. This documentation baseline intentionally excludes source, manifests, migrations, and build files from its Git commit. Paths below describe the observed local scaffold; they may not exist in a documentation-only checkout.

The scaffold currently wires a health route, configuration loading, logging, and process lifecycle. It does not establish completed enrollment, E2EE delivery, quota enforcement, or storage integration. Empty directories and dependency declarations are not implementation evidence. The technology baseline and reusable code remain subject to [Plan 00](../docs/plans/00-core-contracts-and-technology.md).

## Current local path map

Paths in this table are relative to `server/`.

| Path | Current content or intended responsibility | Status |
| --- | --- | --- |
| `cmd/tunnel-server/main.go` | Load configuration/logger, construct the application, handle termination, and request shutdown. | Existing entry point |
| `internal/app/app.go` | Compose the HTTP router/server and expose application start/stop. | Existing bootstrap |
| `internal/app/logger.go` | Select the development or production Zap logger. | Existing logger construction |
| `internal/config/config.go` | Typed environment configuration and parsing helpers. | Existing loader |
| `internal/transport/httpapi/router.go` | Chi router, shared middleware, and handler registration. | Existing route composition |
| `internal/transport/httpapi/handler/` | HTTP handlers, route registration, and JSON response helper. | Health handler exists; user handler is an unwired stub |
| `internal/transport/httpapi/middleware/` | Request-level transport behavior. | Request logging exists |
| `internal/transport/websocket/` | Reserved transport location. | Empty; no WebSocket protocol is selected by this directory |
| `internal/domain/` | Reserved location for domain types, rules, and state transitions. | Empty |
| `internal/usecase/` | Reserved location for coordinating domain operations. | Empty |
| `internal/adapter/postgres/` | Reserved PostgreSQL persistence adapter location. | Empty |
| `internal/adapter/mailer/` | Reserved external mail adapter location. | Empty |
| `migrations/000001_init_tentserv_tunnel.sql` | Existing draft schema. | Not evidence of an accepted schema or applied migration |
| `sql/queries/`, `sql/seeds/` | Reserved SQL assets. | Empty; `sql/README.md` is also empty |
| `test/` | Reserved component/integration test location. | Empty |
| `deploy/Dockerfile` | Reserved container build file. | Empty |
| `go.mod`, `go.sum` | Local module and dependency declarations. | Existing manifests |

## Coding patterns and layer boundaries

Observed patterns to consult when extending the scaffold:

- Bootstrap dependencies through constructors such as `NewApp`, `NewRouter`, and `NewHealthHandler`. Pass configuration and loggers explicitly.
- Compose handler routes through `RegisterRoutes(chi.Router)` and assemble them in the router. Check registration as well as the existence of a handler file.
- Share HTTP JSON encoding through `handler.WriteJSON`, which encodes before writing the response status. Callers handle the returned error.
- Use typed configuration in `internal/config` and structured Zap fields for logging. Startup and shutdown orchestration belong to the entry point and application bootstrap.
- The existing code uses explicit error returns and `context.Context` for shutdown. Review adjacent behavior before extending it; a scaffold is not proof that every error path or lifecycle guarantee is complete.

Boundary guidance for the reserved layers, to refine in the owning plan:

- Keep HTTP decoding, validation of transport syntax, response mapping, and middleware in the transport layer.
- Put domain state transitions and invariants in domain/use-case code. Keep database-driver calls and external-provider details in adapters rather than duplicating rules in handlers.
- Define persistence operations around required atomic behavior. PostgreSQL and SQLite must preserve the same accepted domain contracts; exact interfaces and additional driver paths are pending.
- Persist opaque ciphertext and routing/accounting metadata. Logs must not acquire payload plaintext, endpoint secrets, or secret-bearing pairing material.
- Follow the nearby naming and package structure. Agree on formatter/linter settings in Plan 00; do not reformat or rename unrelated hand-written files while implementing a feature.

## Finding a feature

This table identifies starting points and plan ownership, not implemented feature claims. New packages should be assigned explicit paths when their plan is discussed, then recorded here.

| Work area | Start here in the local scaffold | Owning plans |
| --- | --- | --- |
| Startup and configuration | `cmd/tunnel-server/`, `internal/app/`, `internal/config/` | [00](../docs/plans/00-core-contracts-and-technology.md), [12](../docs/plans/12-deployment-and-product-acceptance.md) |
| HTTP endpoints and errors | `internal/transport/httpapi/` | [01](../docs/plans/01-e2ee-text-baseline.md), [11](../docs/plans/11-api-cli-and-extension-contracts.md) |
| Enrollment, authentication, owner binding, quota | Transport entry points, then reserved `internal/usecase/`, `internal/domain/`, `internal/adapter/` | [03](../docs/plans/03-accounts-authentication-and-quota.md) |
| Pairing relay, public material, endpoint authorization | Transport entry points and reserved domain/use-case layers | [01](../docs/plans/01-e2ee-text-baseline.md), [05](../docs/plans/05-endpoint-and-key-lifecycle.md) |
| Text acceptance, receipts, retries, cancellation | Reserved domain/use-case and persistence layers | [01](../docs/plans/01-e2ee-text-baseline.md), [02](../docs/plans/02-delivery-durability-and-recovery.md) |
| Database and local ciphertext persistence | `internal/adapter/postgres/`, `migrations/`, `sql/`; SQLite/content-store paths remain unassigned | [04](../docs/plans/04-storage-backends-and-consistency.md) |
| Chunk and reliable-stream buffering | Reserved domain/use-case layers and future content-store implementation | [06](../docs/plans/06-chunk-transfer.md), [07](../docs/plans/07-reliable-stream.md) |
| Window retention and eviction | Reserved domain/use-case layers and future content-store implementation | [08](../docs/plans/08-fixed-capacity-window.md) |
| Relay support for TCP forwarding | Shared authorization/transport behavior; socket adapters belong to `app/` | [09](../docs/plans/09-tcp-forwarding.md) |
| Object storage | Future adapter behind the content-store contract; concrete path pending | [10](../docs/plans/10-object-storage-backend.md) |
| Packaging and integration verification | `deploy/`, `test/`, and feature-owned checks | [12](../docs/plans/12-deployment-and-product-acceptance.md) |

## Scaffold discrepancies to resolve explicitly

- The local Go module/import prefix currently uses `github.com/HiroLaing/tentserv-tunnel/server`, while the repository owner is `HiroLiang`. Resolve that naming discrepancy in an authorized source change; the documentation task does not rewrite imports or the module.
- Configuration currently requires `DATABASE_URL`, but bootstrap does not initialize a database adapter. Do not infer working database support from the configuration or dependencies.
- The SQL draft predates completed plan discussions. Review it against accepted ownership, identity, state, and backend contracts before treating it as the schema baseline.

## Keeping this guide useful

When an authorized feature introduces or moves a package, update its actual path, implementation status, and owning plan here. Record test evidence and completion in the relevant plan. Keep speculative paths clearly labeled, and keep application-specific examples in the root README.md.
