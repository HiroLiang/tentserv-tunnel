# Project Implementation Plan

Status: Roadmap structure approved; individual plans are drafts for sequential discussion.
Updated: 2026-09-17

[Planning index](README.md) · [Project instructions and decisions](../../AGENTS.md)

## Purpose and authority

This is the root plan for completing the currently agreed product. It links the child plans, assigns their estimated effort, records dependencies and acceptance gates, and defines when to establish Git checkpoints.

AGENTS.md remains the source of project interaction rules and confirmed requirements. This root plan organizes implementation; child plans refine their own tasks and evidence. Unresolved choices remain explicit until discussed. All development documentation uses English and describes generic behavior only. Application usage examples belong in the repository root README.md.

The user authorized creation of this plan structure. Its existence does not itself authorize implementation commands, Git mutations, or changes to unnamed source files under the current project rules.

## Product completion scope

- Default cloud and configurable self-hosted deployments with independently identified endpoints.
- Email OTP or TOTP account verification, an extensible authentication boundary, and the self-hosted profile without external user verification.
- PAKE pairing with endpoint-generated secrets handed off independently of the relay, authenticated peer identities, E2EE sessions, and defined key/endpoint lifecycles.
- Account-wide sender ownership, capacity reservation/usage/release, and quota enforcement across endpoints and channels.
- Text, resumable chunk transfer, reliable resumable stream, and fixed-capacity window primitives.
- Authorized bidirectional TCP forwarding with explicit connection lifecycle guarantees.
- API and CLI operations for the supported capability set.
- PostgreSQL 18 cloud metadata, SQLite self-hosted metadata, local ciphertext files, and a selected object-storage backend.
- Restart/retry/cancellation/expiry behavior, migrations, cleanup, diagnostics, and supported deployment procedures.

Window eviction is intentional loss of old retained data; it must not be applied to reliable streams. TCP recovery does not recreate an already terminated external socket. Transport receipt is distinct from external application completion.

Future billing can use owner/usage attribution. Pricing, subscriptions, and payment collection have not been agreed as implementation requirements. Additional topology, protocol, or protection requirements must be scoped and estimated before being added.

## Child plans and effort allocation

These percentages are initial engineering-effort estimates, not measured implementation progress, source-line percentages, or calendar commitments. They include each plan's design refinement, code, relevant tests, integration, and technical documentation. Shared work is charged to the plan that introduces it; later plans reuse it.

The existing implementation has not been audited for completeness. Plan 00 will inventory reusable work within authorized access and update these estimates after language/library decisions. Each child is initially a draft; no implementation completion is claimed.

| Plan | Work area | Estimated share | Cumulative in listed order | Prerequisites | Status |
| --- | --- | ---: | ---: | --- | --- |
| [00](00-core-contracts-and-technology.md) | Core Contracts and Technology | 5% | 5% | None | Draft for discussion |
| [01](01-e2ee-text-baseline.md) | E2EE Text Baseline | 12% | 17% | [00](00-core-contracts-and-technology.md) | Draft for discussion |
| [02](02-delivery-durability-and-recovery.md) | Delivery Durability and Recovery | 11% | 28% | [01](01-e2ee-text-baseline.md) | Draft for discussion |
| [03](03-accounts-authentication-and-quota.md) | Accounts, Authentication, and Quota | 10% | 38% | [02](02-delivery-durability-and-recovery.md) | Draft for discussion |
| [04](04-storage-backends-and-consistency.md) | Storage Backends and Consistency | 9% | 47% | [03](03-accounts-authentication-and-quota.md) | Draft for discussion |
| [05](05-endpoint-and-key-lifecycle.md) | Endpoint and Key Lifecycle | 8% | 55% | [04](04-storage-backends-and-consistency.md) | Draft for discussion |
| [06](06-chunk-transfer.md) | Chunk Transfer | 9% | 64% | [05](05-endpoint-and-key-lifecycle.md) | Draft for discussion |
| [07](07-reliable-stream.md) | Reliable Stream | 10% | 74% | [06](06-chunk-transfer.md) | Draft for discussion |
| [08](08-fixed-capacity-window.md) | Fixed-Capacity Window | 8% | 82% | [07](07-reliable-stream.md) | Draft for discussion |
| [09](09-tcp-forwarding.md) | TCP Forwarding | 7% | 89% | [07](07-reliable-stream.md) | Draft for discussion |
| [10](10-object-storage-backend.md) | Object Storage Backend | 4% | 93% | [04](04-storage-backends-and-consistency.md), [06](06-chunk-transfer.md), [07](07-reliable-stream.md), [08](08-fixed-capacity-window.md), [09](09-tcp-forwarding.md) | Draft for discussion |
| [11](11-api-cli-and-extension-contracts.md) | API, CLI, and Extension Contracts | 3% | 96% | [10](10-object-storage-backend.md) | Draft for discussion |
| [12](12-deployment-and-product-acceptance.md) | Deployment and Product Acceptance | 4% | 100% | [11](11-api-cli-and-extension-contracts.md) | Draft for discussion |
| **Total** | **Agreed product engineering effort** | **100%** | **100%** | | |

The root plan is the authoritative effort table. Keep each child's percentage synchronized when revising estimates. Index/root-document creation is not a separate product-completion percentage.

Estimate assumptions:

- Plan 01 uses a local SQLite self-hosted profile to establish the first E2EE text path; Plan 04 completes PostgreSQL parity and broader storage consistency.
- Required security and authorization are part of the first path; later plans complete recovery, account, and lifecycle coverage.
- API/CLI operations and feature-level verification ship with each feature. Plan 11 is consolidation, not all interface implementation.
- Tests and failure checks are distributed throughout the roadmap. Plan 12 is integrated acceptance and packaging, not deferred feature testing.
- Plan 10 assumes one initial object-store compatibility target.
- Supported platforms, instance topology, window cardinality, and operational targets will be fixed or assigned explicitly in Plan 00. Any expansion requires re-estimation.

## Dependency and discussion order

Discuss plans in numeric order, starting with Plan 00. Resolve a plan's blocking decisions before treating it as ready to implement. Later-plan decisions should have an owner and should not unnecessarily block earlier independent work.

Implementation progression:

1. Plan 00 establishes the contracts.
2. Plan 01 produces the first usable E2EE text path.
3. Plans 02–05 complete delivery recovery, accounts/quota, storage parity, and key lifecycle.
4. Plans 06–07 implement chunks and reliable streams.
5. Plans 08 and 09 both build on Plan 07; their order may be exchanged without changing their scope.
6. Plan 10 completes the object-store backend; Plan 11 consolidates interfaces/extensions; Plan 12 accepts the complete product.

Dependency independence is not an instruction to delegate work or start parallel agents.

## Product milestones

| Milestone | Required plans | Observable result | Approximate cumulative effort |
| --- | --- | --- | ---: |
| M1: Text baseline | 00–01 | Two endpoints pair and exchange delayed E2EE text through API/CLI. | 17% |
| M2: Reliable foundations | 00–05 | Text has verified recovery, account verification, owner quota, database parity, and key lifecycle. | 55% |
| M3: Transfer capability set | 00–09 | Text, chunk, stream, window, and TCP forwarding meet their feature acceptance criteria. | 89% |
| M4: Product acceptance | 00–12 | Supported storage/deployment combinations and public interfaces pass integrated acceptance. | 100% |

Cumulative effort labels describe this estimate's distribution. A milestone exists only after its acceptance evidence passes; documents alone do not earn a milestone.

## Requirement ownership and evidence

| Requirement | Primary plan owners | Required evidence |
| --- | --- | --- |
| Generic contracts and threat model | 00 | Explicit states, boundaries, versions, and requirement coverage. |
| E2EE text and initial pairing | 01 | Two-endpoint delivery, delayed receipt, invalid-peer and tampered-payload rejection. |
| Retry, durable receipt, restart recovery | 02 | Controlled interruption and duplicate/lost-ACK cases at durable boundaries. |
| Cloud/self-hosted authentication and owner quota | 03 | Both verification alternatives, negative authorization, and concurrent accounting. |
| PostgreSQL/SQLite and local payload consistency | 04 | Shared backend contracts, migration checks, and cross-store recovery. |
| Endpoint/key continuity and revocation | 05 | Rotation, delayed receipt, unexpected identity change, and revocation checks. |
| Chunk | 06 | Complete-content integrity, resume, deduplication, bounded progress, and cancellation races. |
| Stream | 07 | Ordering, bounded backpressure, durable cursors, authenticated EOF, and recovery. |
| Window | 08 | Bounded retained range, decryptable retained records, explicit gaps, and consistent eviction. |
| TCP forwarding | 09 | Authorized destinations, bidirectional bytes, independent sockets, half-close/reset, and interruption behavior. |
| Object storage | 10 | Applicable content-store conformance and failed-upload/deletion reconciliation. |
| Complete API/CLI and provider contracts | 11 | Operation coverage, consistent errors, provider failures, and sensitive-output checks. |
| Deployable product | 12 | Reproducible supported profiles, lifecycle/upgrade verification, and full traceability. |

## Plan status and completion rules

Use explicit statuses: draft for discussion, ready, in progress, verifying, or complete. A blocked item records its blocking decision rather than silently reducing scope.

A child is complete when:

- Its required decisions and interfaces are recorded.
- Its deliverables are implemented within the authorized scope.
- Its acceptance checks pass against the supported configurations relevant to that plan.
- Failure behavior, limitations, and evidence are recorded.
- Required follow-up work is completed or explicitly reassigned without leaving a root requirement unowned.

The complete product requires all root requirements to have passing evidence or an explicitly agreed scope change. The roadmap does not claim cryptographic assurance solely from happy-path tests; the selected composition and lifecycle also require review against the threat model.

## Git establishment and checkpoints

### Observed state

A .git directory exists at the workspace root. This was observed through read-only filesystem metadata. Git history, branch state, tracked files, and working-tree changes have not been inspected with Git, and no Git mutation has been performed for this planning request.

### G0: Establish the baseline now, before Plan 00 implementation

Use the existing repository; do not reinitialize it or replace its history. At the next authorized Git step:

1. Inspect the current branch, history, and working tree.
2. Preserve existing hand-written work and stage only reviewed files.
3. Review exclusions for credentials, private keys, pairing secrets, runtime databases, queues, and stored payloads before staging.
4. Record the planning baseline containing this root, the index, child plans, and relevant project instructions. Include other project files only when reviewed and authorized.
5. If the repository has no commits, this becomes its initial reviewed baseline; otherwise it is a normal commit on the existing history.

If this project is later copied into a directory without Git metadata, initialize version control before its first implementation change. That conditional instruction does not apply to the currently observed existing repository.

### Working checkpoints

| Git point | Timing | Expected checkpoint |
| --- | --- | --- |
| G0 | Planning files completed; before implementation begins | Reviewed existing-repository baseline and safe file exclusions. |
| G1 | Plan 00 decisions accepted | Contract/technology decisions recorded before dependent implementation. |
| Per-plan increments | During each plan | Small coherent implementation changes with relevant verification; keep unfinished work distinguishable from accepted work. |
| Per-plan completion | The plan's acceptance checks pass | Accepted code, tests, interface changes, migrations, and plan status recorded together. |
| M1 | Plans 00–01 accepted | Reproducible E2EE text baseline. |
| M2 | Plans 00–05 accepted | Reproducible reliable-foundation checkpoint. |
| M3 | Plans 00–09 accepted | Complete transfer-capability checkpoint. |
| M4 | Plans 00–12 accepted | Release checkpoint and release tag after final acceptance. |

Suggested implementation branches use the project prefix, such as codex/plan-01-text. Branch names and milestone tags are planning conventions, not actions already taken. Never mark a failing checkpoint as an accepted milestone.

Before a persistent-state migration or incompatible wire-format change, preserve a reviewed checkpoint and the applicable recovery procedure. Commits do not replace runtime data backups or migration recovery.

## Next discussion

Start with [Plan 00](00-core-contracts-and-technology.md): language/platforms, API form, cryptographic composition, protocol contracts, and the unresolved deployment/retention limits. Review one child plan at a time and refine it before implementation.
