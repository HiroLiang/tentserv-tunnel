# AGENTS.md

## Project Interaction Rules

- Unless the user explicitly asks in the current conversation to modify files, do not create, edit, delete, move, format, or otherwise change any files.
- Even when the user asks to modify files, the only file Codex may change in this project is this `AGENTS.md` file, unless the user explicitly names another file and asks for that file to be modified.
- Unless the user explicitly asks in the current conversation to run commands, do not execute shell commands, start servers, install dependencies, run tests, operate Git, or invoke tools that modify local state.
- Treat this project as hand-written by the user for now. Default to answering questions, explaining tradeoffs, reviewing pasted code, or giving suggestions without touching the workspace.
- Write Markdown documents in English. Conversation replies may remain in the user's language.
- Keep application use cases and feature-usage examples in README.md only. AGENTS.md and other development documents must describe generic requirements, interfaces, protocol behavior, and architectural constraints without application-specific narratives, including narratives framed as exclusions.
- If an answer would normally require reading files or running commands, ask for permission first or answer from the context the user provides.

## Repository Navigation and Component Guides

- `server/` is the base directory for the server-side relay, authentication, authorization, quota, and ciphertext-persistence implementation. Read [server/READMS.md](server/READMS.md) before working in this component.
- `app/` is the base directory for the complete user-side endpoint toolchain, including its API/CLI surfaces, local state, E2EE behavior, and forwarding adapters. Read [app/READMS.md](app/READMS.md) before working in this component.
- The component guides use the user-requested filename `READMS.md`. They map internal responsibilities, feature locations, existing coding patterns, and unresolved scaffold details; they do not replace this file's interaction rules or the implementation plans.
- Start with this file, open the relevant component guide, and then read the owning plan and nearby source before changing a feature. For cross-component changes, consult both guides and preserve the shared protocol contract.
- Distinguish observed local source, empty directories, draft module names, and accepted implementation decisions. A path or dependency in a scaffold does not establish that a feature is implemented or that its design is finalized.
- Keep component maps aligned with authorized structural changes. These guides describe generic architecture and code organization; application use cases remain in the root README.md.

## Implementation Planning

- Use [docs/plans/README.md](docs/plans/README.md) as the entry point for implementation plans and their index.
- Follow the [root project plan](docs/plans/project-plan.md) for the agreed implementation breakdown, child-plan links, estimated effort allocation, acceptance milestones, and Git checkpoints.
- The planning directory describes how the currently agreed requirements will be implemented. Keep project interaction rules and confirmed direction in this file.
- Discuss the proposed implementation breakdown in the conversation first. Create individual plan documents only after the user confirms the breakdown.
- The user approved creation of the root and Plans 00–12. Discuss each child plan's unresolved details before implementation; the files begin as drafts, and their effort percentages are estimates rather than measured completion.

## Project Direction Notes

- Use this section to record the project's goals, direction, constraints, and design decisions when the user asks Codex to remember them.
- Do not infer or expand the project direction beyond what the user explicitly states.
- Keep entries concise and easy to revise.

### Current Direction

- Discuss purpose, behavior, architecture, and data requirements before implementation.
- Use PostgreSQL 18 for the cloud database and initially support SQLite for self-hosted deployments. Provide a persistence abstraction and configurable supported backend. Store large ciphertext in local files or object storage, with database records indexing its location.
- Devices, virtual machines, and services can connect to the author's default cloud or a configurable self-hosted/internal server.
- The server advertises its enrollment/authentication requirements. The author's cloud requires user verification; self-hosted deployments may omit that verification or integrate their own authentication system through an extension interface.
- Establish endpoint keys and server-side transfer space after enrollment. Keep private keys at the endpoints and publish only the required public key material.
- Establish a two-endpoint channel through a temporary pairing invitation and verification. Pairing will include more than a code-to-public-key lookup.
- Pairing must work without a QR display, including headless Linux. The user's "app rolling key" means a six-digit TOTP code such as those displayed by Microsoft Authenticator. Email OTP and TOTP are alternative account-verification methods, not mandatory combined factors. Detailed enrollment/recovery remains open.
- The user accepted endpoint-generated, single-use text-secret pairing through PAKE, with the secret conveyed over a trusted channel outside the relay's control. The precise protocol/library and handoff integration remain to be specified.
- The threat model includes a malicious or compromised relay actively substituting peer public keys. Pairing must establish endpoint identity without relying solely on that relay's assertions.
- Devices and services may register independent endpoint identities. Endpoints under the same verified account, such as the same verified email account, share that account's storage allocation.
- Charge queued ciphertext to the sending account A, aggregated across its endpoints and channels. Opening another channel must not bypass the account quota. Future cloud billing should be attributable to that sending account.
- Provide API and CLI access, including TCP/service-port forwarding. The user explicitly requested adding TCP forwarding to the planned feature scope.
- Provide a fourth generic data-transfer primitive, window, alongside text, chunk, and stream. A producer opens a fixed-capacity window and continuously pushes opaque stream-like data into it; old retained data is displaced as necessary to keep the window bounded.
- The service must remain application-agnostic. Users determine how payloads are interpreted; the service provides generic encrypted transfer, authorization, and storage behavior without application business logic.
- Use the standard term "symmetric key." The user's earlier phrase "compressed key" meant a symmetric key, not compression. The earlier Sender Key versus X3DH mode labels are not finalized protocol choices.
- Provide text messages, chunk transfers, streams, and windows with E2EE. Plaintext communication content belongs only at the intended endpoints, including service-to-service endpoints.
- Support delayed receipt through server-side ciphertext storage. Text sends fail when capacity is insufficient. Chunk transfers wait for capacity, release acknowledged chunks, support resumption, and delete the transfer's remaining server data on cancellation.
- The user accepted the proposed transfer handling refinements: atomic message acceptance/rejection, retry deduplication, verification and durable receipt before storage-release acknowledgment, resumable chunks, and explicit ordering/end/disconnection rules for streams.
- Use bounded server-buffered reliable streams; live-only mode is not required. Support interruption/resumption while a transfer remains valid, unless it expires or cancellation is confirmed. Notify senders to pause, resume, or terminate as appropriate.
- Keep requirements, assessments, decisions, and open questions in this file for continuity. Organize confirmed implementation plans under [docs/plans/](docs/plans/README.md). Write all Markdown documents in English.

## Design Discussion Record — 2026-09-17

This record consolidates the initial proposal and subsequent decisions. "Current Direction" contains user-stated or accepted requirements. API plus CLI with TCP forwarding, text/chunk/stream/window primitives, sender-account quotas, resumable buffered streams, PAKE text-secret pairing, alternative email OTP/TOTP verification, PostgreSQL 18 for cloud, SQLite for initial self-hosting, and external large-ciphertext storage are decided. The product is an application-agnostic encrypted transport and bounded-storage service. Recommendations are labeled separately. Implementation language, API shape, cryptographic library, concrete object-store integrations, deployment topology, and first-use account enrollment remain open.

### Confirmed Decision Register

The following requirements have been confirmed by the user and recorded. This is a requirements record, not an assertion that implementation or all technical choices are complete.

| Area | Confirmed decision |
| --- | --- |
| Product | E2EE device/VM/service communication through a default cloud or configurable self-hosted relay |
| Entry points | API and CLI, including TCP/service-port forwarding |
| Endpoint identity | Independent identities/keys per registered device or service; multiple endpoints may share an account |
| Account verification | Email OTP or TOTP as alternatives; self-hosted deployments may omit user verification or integrate their own provider |
| Pairing | Headless-compatible PAKE with an endpoint-generated one-time secret conveyed outside relay control; resist active relay key substitution |
| Quota ownership | Sending account pays, aggregated across all its endpoints and channels; account is the future billing subject |
| Text and chunks | Atomic text acceptance/rejection, deduplicated retry, durable receipt ACK, resumable chunks, race-safe cancellation/cleanup |
| Native reliable streams | Bounded ciphertext buffering, pause/resume control, interruption recovery, retained progress until completion, expiry, or confirmed cancellation |
| Generic windows | Fixed-capacity opaque stream-like storage that accepts ongoing pushes and evicts old retained data; application interpretation belongs to users |
| Product boundary | Application-agnostic behavior; TCP forwarding and generic data primitives provide the integration surface |
| Metadata persistence | PostgreSQL 18 for cloud; initial SQLite support for self-hosting; configurable backend abstraction |
| Large payload persistence | Local files or object storage with database location indexes |
| Documentation | Keep requirements and decisions in AGENTS.md, implementation plans under docs/plans/, and application examples only in the root README.md; write all Markdown in English |

Still-open details include the specific PAKE/session libraries, API form and language, first-use TOTP enrollment/recovery, self-hosted owner provisioning, exact handoff adapter contract, payload drivers, retention defaults, failure-durability targets, and detailed schema. Do not describe those proposals as already confirmed.

### Feasibility, Scope, and Tradeoffs

- Working product description: a self-hostable E2EE communication and transfer layer with delayed delivery. This is an architectural summary of the proposal, not an additional feature commitment.
- The generic capabilities are message delivery, bounded-object transfer, resumable ordered streams, fixed-capacity windows, and authorized TCP forwarding.
- When both endpoints can make outbound connections to the relay, they generally do not need exposed inbound service ports. Firewall restrictions and relay availability still apply.
- TCP forwarding is a selected upper-layer feature. Buffering does not make arbitrary existing TCP applications tolerate long outages or transparently restore their original connections.
- Benefits: endpoints need not always be online together; cloud and self-hosted deployments can share a protocol; the relay can store and deliver ciphertext without content decryption keys.
- Costs: relay bandwidth and storage, endpoint key/state management, crash recovery, and delivery lifecycle complexity. The server cannot directly search or process encrypted content.
- The difficult cases are concurrent quota updates, lost acknowledgments, retries, cancellation races, endpoint crashes, cryptographic state rollback, and payload cleanup.

### Identity and Security Boundaries

- Separate account identity and quota ownership, cryptographic endpoint identity, and authorization to access a channel.
- Email verification primarily proves access to an email account. It does not by itself prove a person's real identity or establish that a peer public key belongs to the intended endpoint.
- Self-hosted deployments may omit user/account verification. They still need an explicit policy for endpoint credentials, channel read/write/delete permissions, and resource limits.
- The authentication extension can advertise supported methods, issue challenges, verify responses, and map results to principals and policies. It should not receive endpoint E2EE private keys or require clients to execute arbitrary server-supplied code.
- No QR requirement: a textual invitation or verification URL/code can be completed on another trusted device. A device authorization flow is a useful enrollment reference, but is not automatically an E2EE peer-authentication protocol. [RFC 8628](https://www.rfc-editor.org/info/rfc8628/)
- "App rolling key" has been resolved to TOTP: the authenticator and verifier use a shared secret and time to generate/check a short-lived code. This is different from a device-held asymmetric signing key. A verifier that possesses the TOTP secret can also produce codes. [RFC 6238](https://datatracker.ietf.org/doc/html/rfc6238)
- Define whether email OTP or app approval authorizes account enrollment, a particular pairing transaction, or both. Pairing approval should be bound to the intended endpoint identities, request, purpose, expiry, and channel context rather than be a reusable generic approval.
- The user requires protection against active relay key substitution. If the relay also controls OTP issuance/verification, OTP approval alone does not meet that requirement. Account login and end-to-end peer authentication must have separate trust boundaries. [X3DH authentication](https://signal.org/docs/specifications/x3dh/#41-authentication)
- Content confidentiality and metadata privacy are separate requirements. The current architecture may reveal account identifiers, IP addresses, routing relationships, timing, and ciphertext sizes. Additional metadata protection remains undecided. [Signal sealed sender](https://signal.org/blog/sealed-sender/)
- A relay can still withhold, delay, or discard traffic. Content secrecy assumes correct implementations, authenticated peers, and uncompromised endpoint secrets.
- For a service-process E2EE boundary, an in-process SDK can own encryption. A local daemon performing encryption also becomes part of the plaintext trust boundary; that deployment choice remains open.

### Accepted Headless Pairing Approach — Detailed Protocol Pending

- Accepted user flow: one endpoint generates a single-use random pairing secret locally; the user conveys it to the intended peer over a confidential, authenticated channel outside the relay's control; the endpoints use a mature PAKE-based pairing design, confirm the exchange, and authenticate/bind their long-term identity keys and pairing context.
- A public invitation locator can be sent to the relay to find the pending exchange. The secret part must not be uploaded as a plaintext lookup code, logged remotely, or issued by the untrusted relay. Delivering the secret through the same compromised server's email-OTP service defeats this independence.
- Use a reviewed protocol/library rather than inventing a PAKE-plus-X3DH composition. PAKE is a candidate for authenticating the initial pairing; later asynchronous session establishment must use the peer identity keys authenticated during pairing. [SPAKE2 RFC 9382](https://www.rfc-editor.org/rfc/rfc9382.html)
- Magic Wormhole demonstrates a CLI-friendly PAKE pairing flow with text codes and a relay. It is a design reference, not a commitment to its code length, transport, or implementation. [Magic Wormhole documentation](https://magic-wormhole.readthedocs.io/en/latest/welcome.html)
- Prefer adequate entropy in a pasteable secret or random word sequence. PAKE prevents passive offline password testing under its assumptions but does not remove active guessing. Enforce single use, expiry, and a bounded failure budget at the endpoints, across reconnects; do not rely only on rate limits enforced by a potentially malicious relay.
- Persist verified peer identity keys locally. Unexpected key changes pause the channel for trusted re-verification or an authenticated key-rotation process. An email account reset must not silently replace a previously trusted peer key or recover old E2EE secrets.
- Pairing requires completion of the necessary interactive exchange and key confirmation before the channel becomes usable. Subsequent buffered delivery can remain asynchronous; re-pairing is not needed for every resumed transfer.
- Simpler alternative: compare endpoint-generated fingerprints/safety numbers through an independent authenticated channel. Terminal text is sufficient; a QR display is optional.
- Alternative for future trusted-device enrollment: an already trusted app signs an approval bound to the new endpoint key and pairing request using a private key held by that app. Verifiers must already have an independently authenticated trust anchor for the app; fetching its public key only from the untrusted server recreates the original problem.
- Key transparency can be considered later as defense in depth; it is not a substitute for defining first-contact and account-recovery trust.

### Self-Hosted Enrollment and Pairing Handoff

- Disabling email/TOTP account verification does not disable cryptographic endpoint identity, peer pairing, or channel authorization. Self-hosted endpoints still establish a verified relationship before exchanging protected application data.
- PAKE has two endpoint participants. A relay transports protocol messages, and an optional delivery system carries an invitation; neither role is inherently a third-party PAKE verifier.
- The same operator can read the secret on A and enter it on B through an existing trusted session. A second person, mobile app, QR display, or external verification provider is not required.
- A and B may share a public invitation locator through the relay. The secret is supplied independently at the endpoints and is not uploaded in plaintext to the relay.
- Proposed extension split: an account-authentication provider belongs to deployment enrollment; an invitation-handoff adapter belongs to the client or a separately trusted provisioning environment; the PAKE/key-confirmation implementation remains in the cryptographic core.
- Initial handoff options can be manual terminal display/input and local invitation export/import. Extension adapters must define their independent authentication and confidentiality guarantees, execution boundary, and secret-handling contract. Concrete adapters remain undecided.
- An external handoff service that receives the plaintext pairing secret becomes part of the pairing trust boundary. A plugin executing on the malicious relay cannot independently guarantee secrecy from that relay; an independently authenticated encryption recipient would be needed to keep an external delivery service blind.
- Handoff interfaces should distinguish public locator/context from secret-bearing material and specify expiry, single-use handling, delivery/claim status, and sensitive-log redaction. Do not let a server-provided plugin replace the client's peer-verification rules.
- Self-hosted quota owners can be provisioned locally by an administrator without collecting a real-world identity. Endpoints still need an authorized binding to that owner; allowing arbitrary owner IDs would undermine quota isolation.
- TOTP-only enrollment must establish a stable account identifier and securely provision its shared secret before login verification is meaningful. It does not independently verify email ownership; shared quota follows the account, including when no email is present. [RFC 6238](https://datatracker.ietf.org/doc/html/rfc6238)

### Account Ownership, Quota, and Billing

- Separate the account/owner, endpoint identity, channel, and transfer entities. A single owner may have many endpoints with different cryptographic keys and many channels.
- Attribute each transfer to the sending endpoint's owner. Aggregate stored ciphertext plus reserved in-flight capacity over all that owner's endpoints/channels before granting more upload credit.
- Bind verified authentication identities to a stable internal owner ID. Do not trust a client-supplied email string or let an endpoint choose an arbitrary billing owner. Account linking/normalization requires an explicit verified policy.
- Returning traffic from B is a new sending direction attributable to B's owner. Per-channel limits can supplement but cannot replace the owner-wide quota.
- Release capacity exactly once for acknowledged/cancelled/expired data. Track reservations and stored bytes consistently so concurrent transfers cannot all spend the same remaining capacity.
- Future paid plans attach to the owner. Pricing and metering dimensions are not yet chosen; repeated requests must not accidentally count the same logical storage allocation multiple times.
- Self-hosted deployments without email still need a way to assign endpoints to quota owners, such as administrator-provisioned owners or enrollment credentials. The specific mechanism is open.

### Cryptographic Roles and Candidate Design

- X3DH establishes a shared secret. Its identity keys, signed prekeys, one-time prekey pool, and initiator ephemeral key have different lifecycles. It is not the continuing data-transfer cipher. [X3DH specification](https://signal.org/docs/specifications/x3dh/)
- Recommendation, not yet selected: a mature authenticated session establishment protocol followed by Double Ratchet for ordinary messages and control payloads. Ratcheting changes message keys; recovery after compromise requires appropriate fresh secret inputs and secure state handling. [Double Ratchet](https://signal.org/docs/specifications/doubleratchet/)
- Recommendation for bulk data: evaluate a separate symmetric key per transfer, delivered through an authenticated E2EE session, with a mature authenticated chunk/stream format. Do not rerun X3DH for every chunk or treat a permanent shared channel key as equivalent to a ratcheted session.
- The formal Sender Keys mechanism concerns group messaging. The user's clarified requirement is symmetric-key exchange; adopting Sender Keys or supporting groups has not been decided. [Sender Keys research](https://arxiv.org/abs/2301.07045)
- A retained whole-transfer key can expose the whole corresponding transfer if compromised. Per-message ratcheting and per-transfer encryption have different compromise scopes.
- Require authenticated frame contents and an authenticated final marker. Sequential streaming formats such as libsodium secretstream are references, not a selected library; random access, parallel download, and resumption need compatible format/state design. [libsodium secretstream](https://libsodium.gitbook.io/doc/secret-key_cryptography/secretstream)
- Protect the negotiated version/algorithms, endpoint identities, directions, and channel/transfer context at the appropriate protocol layer. Prevent downgrade, replay, and cross-channel substitution through a reviewed protocol composition.
- Long-term post-quantum confidentiality is an open requirement. Classical X3DH does not provide that guarantee; PQXDH and subsequent ratchet choices would require separate evaluation. [PQXDH](https://signal.org/docs/specifications/pqxdh/)

### Candidate Architecture and Lifecycle

Endpoint A [application/SDK, keys/session, local queue] <-> TLS <-> Relay [authentication/pairing, public-key directory, routing, quota/delivery state, ciphertext storage] <-> TLS <-> Endpoint B [receive state, keys/session, application/SDK].

- These are logical responsibilities, not a requirement for separate microservices. A single server process remains a candidate for simple self-hosting.
- Keep deployment authentication separate from the E2EE core so cloud verification and custom self-hosted providers can share transfer behavior.
- Enrollment: authenticate the server and discover supported policy/version -> perform the deployment's user verification -> generate local endpoint secrets and register public material -> establish transfer-space ownership.
- Pairing: issue an expiring invitation -> submit a request -> authenticate the intended peer identities and approve the bound request -> establish a session -> activate the channel.
- Offline cryptographic session initiation and human approval are separate. If approval is required, a pending encrypted request does not mean the user has accepted the pairing.
- Delivery: encrypt locally -> relay durably accepts ciphertext -> recipient fetches and verifies it -> recipient durably records receipt -> acknowledge -> release relay payload and quota.
- Distinguish relay acceptance, durable endpoint receipt, and application/business completion. A durable transport ACK does not prove that an external business operation completed exactly once.
- The default ACK recommendation now accepted is verification plus durable receipt before relay data removal. The public API may expose the separate delivery milestones; its exact shape remains open.

### Accepted Transfer Handling

| Type | Behavior | Reliability rules |
| --- | --- | --- |
| Text message | Reject when capacity is insufficient | Atomically accept or reject the complete message; use stable IDs for safe retry/deduplication |
| Chunk transfer | Wait when full; release received chunks; support resumption; cancel remaining server payload as a group | Track transfer/chunk identities and progress; verify and durably receive before ACK; retain the information needed to resume |
| Stream | Buffer ciphertext within limits; pause/resume the sender; retain resumable progress until expiry or confirmed cancellation | Ordered authenticated data, authenticated ending, durable progress, bounded buffering, explicit recovery, and cleanup; no live-only mode required |
| Window | Retain a fixed-capacity rolling range of opaque data; admit ongoing pushes by evicting older retained data | Explicit retained range, sequence/cursor handling, eviction/gap reporting, E2EE, and owner-wide capacity accounting; detailed API semantics remain open |

- "Text" is an application payload type; its uploaded content is still ciphertext.
- Proposed distinction: chunks belong to a bounded object/file; a stream is ordered data whose final length may be unknown and whose valid pending data is retained for delivery; a window retains only a bounded recent range and intentionally evicts older data. They may share encrypted records and transport machinery without identical application semantics.
- Use backpressure rather than an unbounded local queue. A non-pausable source, a receiver that falls behind, and finite storage cannot simultaneously guarantee no data loss and bounded latency.
- Data not yet accepted by the relay remains dependent on sender-side retention. A sender cannot go offline after handing over an arbitrarily large transfer when server capacity is insufficient.
- To transfer an object larger than the quota window, the receiver must consume and acknowledge chunks before the full object has uploaded; a single chunk must fit the usable window.
- Account for actual stored bytes, including encryption overhead. Treat tenant quota exhaustion separately from global physical storage exhaustion.
- Reserve resources for reads, ACKs, cancellation, and cleanup so a full data quota does not prevent releasing space.
- Make quota reservations, commit, and release safe under concurrent uploads and repeated ACK/cancel requests. The same bytes must not be released twice.
- Use stable message/transfer/chunk identifiers and recipient deduplication. Network acknowledgments alone do not provide exactly-once external application effects.
- Cancellation should first record a terminal state and reject subsequent writes, then reclaim payloads. Retain sufficient terminal-state information to prevent late retries resurrecting cancelled data.
- Deletion governs cooperative server storage and quota. It cannot revoke copies already obtained by the recipient or prove that a malicious server did not retain ciphertext. Backup/version retention requires a separate policy.
- Bound invitation lifetimes, incomplete uploads, unread data, local queues, orphaned payloads, and waiting periods.

### Accepted Stream Policy and Proposed Recovery Mechanics

- Buffered reliable streams are selected. Receive online when possible; otherwise retain accepted encrypted frames within per-stream and sending-owner quotas. Do not silently discard old frames to make room.
- Live-only mode is not required. An offline recipient or transport disconnection alone does not cancel the transfer.
- A stream should have a byte limit, expiry, maximum idle/wait interval, and sender-side queue limit. An available tenant quota alone is not permission for unbounded buffering.
- Recommend server-issued byte credits or equivalent enforced upload windows backed by atomic quota reservations. The sender transmits only within its granted window; zero credit means stop uploading and propagate backpressure to the producer. Include already-authorized in-flight data in accounting.
- Proposed control behavior: PAUSE(reason) is reversible; RESUME or a fresh credit grant permits further upload; CANCEL/ABORT(reason) is terminal. Reasons can include capacity exhaustion, receiver policy, expiry, or explicit cancellation. Wire names remain undecided.
- Control notifications are hints backed by queryable state and enforced admission limits. A lost notification or broken connection must not let the sender upload without authorization or wait forever.
- Preserve accepted data and progress across interruptions. Reconnect, reauthorize, reconcile durable receive positions, deduplicate retries, and resume. A peer-receipt claim must be authenticated end to end if the sender relies on it; a relay claim alone does not prove that the peer received data.
- Distinguish producer completion (authenticated EOF sent), relay acceptance of EOF, recipient durable completion, and cleanup. EOF is not the same as cancellation.
- On explicit cancellation or terminal expiry, stop new admissions, notify reachable peers, record the terminal reason, and clean up remaining relay payload with race-safe quota release. Already delivered content cannot be recalled.
- Plan durable endpoint state so process restart does not inherently discard a valid stream. The exact cryptographic framing remains to be selected: it needs durable checkpoints or independently resumable authenticated segments with consistent ordering and a verified final marker.
- Persist the sender outbox and encryption progress consistently. Retries should resend the same committed ciphertext for a frame; never roll back counters and reuse a key/nonce for different plaintext. Persist receiver verification/deduplication progress before ACK.
- Resume from authenticated, durable progress rather than only a socket byte count. Local state corruption or permanent storage loss must surface as an explicit unrecoverable error, not a fabricated successful resume.
- Network reconnects should not silently reset an absolute expiry or turn a single-use pairing/transfer into an indefinitely renewable one; define expiry and idle-time semantics separately.
- Transport recovery covers persisted payloads and delivery positions. Recovery of an application's internal computation requires application-owned state and is not guaranteed by transport resumption.

### API, CLI, and TCP Forwarding Scope

- API plus CLI with TCP forwarding is the selected user-facing scope. The exact API form (in-process library, local agent API, protocol API, or a combination) and implementation languages remain open.
- A TCP forwarding adapter maps an explicitly authorized local listener to an explicitly authorized peer-side destination through an encrypted channel, without requiring the connected software to implement the native message API.
- Forwarding is an adapter over the secure transport, separate from the transport protocol selected for client-to-relay communication.
- The forwarding adapters are the encryption endpoints. Local legs to the existing services may carry plaintext unless the service's own protocol adds encryption; this affects the service-to-service trust boundary.
- Binding listeners to loopback and authorizing explicit target services would be sensible defaults. No public-port exposure or arbitrary relay-selected destination should be implied.
- Resuming relay frames does not automatically restore a broken application TCP session after endpoint process restart. Long buffering can also exceed an application's timeout. This adapter needs its own documented connection-lifecycle rules while native buffered transfers retain their resumption semantics.
- Proposed adapter requirements: explicitly authorized target services, independent logical stream IDs for concurrent sockets, ordered bidirectional forwarding, backpressure, connect errors/timeouts, half-close and reset handling, and per-direction sender-owner accounting.
- Temporary relay disconnection can be hidden only while the adapters retain the corresponding local sockets and the applications tolerate the pause. Do not attach old byte queues to a newly opened application socket and claim transparent recovery.
- Durable receipt by a TCP adapter is not durable receipt or business completion by the target application. Keep these milestones distinct; application-level retries can duplicate non-idempotent operations.

### Application Boundary

- This service supplies generic encrypted transport, authorization, buffering, and bounded storage. It does not inspect or interpret user payloads or implement application business logic.
- Application-specific examples belong only in README.md and do not establish server requirements.
- A window bounds retained capacity. Capacity and elapsed time are different quantities; any application-defined interpretation belongs to the application.
- Native text/chunk/stream/window APIs carry opaque application data. TCP forwarding adapts an authorized TCP byte stream. Additional transport primitives require explicit scope decisions.
- E2EE protects the relay path. Applications determine how to use or retain plaintext at their authorized endpoints.

### Generic Window — Confirmed Purpose, Detailed Semantics Pending

- Confirmed purpose: an application opens a window with fixed capacity and keeps pushing stream-like data. The retained range advances by discarding old data as new data is admitted. The service does not interpret its content.
- Recommend measuring capacity in stored ciphertext bytes and defining limits for individual records and protocol overhead. The exact accounting convention is pending; a fixed limit bounds retained storage rather than requiring the window to contain that many bytes immediately.
- Recommend reserving the configured capacity against the sending owner's shared quota when opening the window. Creating another window or channel must not bypass that quota. The alternative of charging current occupancy has not been selected; reservation better supports a promised fixed-capacity allocation.
- Proposed lifecycle: create/open with a capacity -> append opaque encrypted records -> read/follow the retained range -> close or delete according to an explicit retention policy. API names and close-versus-delete behavior are not yet finalized.
- Recommend stable window-instance IDs, monotonic record sequences or byte offsets, and separately observable earliest-retained/latest-committed positions. Readers can detect whether their saved cursor still exists.
- Evict the oldest complete encrypted records as needed. An oversized append should fail explicitly unless the client fragments it according to the protocol. Never trim an authenticated ciphertext record arbitrarily and expect it to remain valid.
- Readers may catch up while their cursor remains in the retained range. If it has been evicted, return an explicit gap/evicted-cursor result and the available range; the client/application decides where to restart. Do not claim lossless resumption after eviction.
- Reader ACKs must not automatically extend the window or turn it into an unbounded backlog. Whether the first version supports one or multiple independent readers remains open.
- Continuous append does not eliminate disk/network throughput limits. Apply bounded backpressure or explicit admission failure when writes cannot be sustained, even if old retained data is eligible for eviction.
- Coordinate append publication, eviction, reader access, quota accounting, and crash recovery. Keep temporary staging/deletion overhead bounded and distinguish the logical fixed capacity from physical filesystem/object-store overhead; do not promise exact physical disk usage without defining that overhead.
- Encryption must allow authorized readers to begin at a retained record boundary even after earlier records have been evicted. Select a reviewed record/segment scheme with appropriate key distribution or bounded checkpoints; an unbroken decryption chain requiring discarded data would not satisfy this behavior.
- Reusing a ring-buffer slot must not reuse a key/nonce for new plaintext. Retries need stable identities so they do not advance the window or evict data twice accidentally.
- Record integrity and sequence verification can detect tampering and gaps, but a malicious relay can still withhold data. Deleting relay objects does not revoke plaintext or keys previously copied by authorized recipients.
- A TCP connection can carry window API requests, but an arbitrary existing TCP application expects reliable ordered bytes. A window's eviction behavior cannot be transparently substituted for that application's byte stream; window-aware consumers use the API/protocol and handle explicit gaps.
- Temporal retention, multi-writer coordination, persistence after close, resizing, and subscriber/key-management details remain open. None should introduce application-specific interpretation into the server.

### PostgreSQL 18 and Storage Responsibilities

The user selected PostgreSQL 18 for cloud metadata, SQLite for initial self-hosted metadata, and local files or object storage for large ciphertext. The database indexes payload locations and tracks transfer/owner state. Small-message placement remains an implementation detail to decide.

| Responsibility | Candidate contents | Requirements |
| --- | --- | --- |
| Cloud PostgreSQL 18 | Accounts, endpoints, public prekeys, invitations, channel authorization, transfer state, cursors, quotas/reservations | Uniqueness, atomic state transitions, single-use prekey consumption, idempotency, expiry |
| Ciphertext payload storage | Large encrypted chunks and stream segments | Local files or object storage; efficient reads/writes and incremental cleanup, indexed by metadata records |
| Endpoint persistence | Private keys, ratchet state, outbox/inbox, deduplication and resume state | Secret protection, crash consistency, no nonce/key reuse through state rollback |
| Ephemeral connection state | Online connections, wakeups, credit subscriptions | Recoverable from authoritative state; correctness must survive missed notifications |

- PostgreSQL supports transactions and explicit locking, but capacity checks and subsequent updates must be designed as a safe concurrent operation rather than an unprotected check-then-write sequence. [PostgreSQL 18 transaction isolation](https://www.postgresql.org/docs/18/transaction-iso.html)
- If metadata and payloads use separate storage systems, define reservation, payload write, metadata commit, and recovery/cleanup phases. Do not assume cross-store atomicity.
- One-time prekey allocation must be atomic. Endpoint key/session retention must be compatible with the permitted offline delivery period.
- VM cloning or snapshot rollback can duplicate endpoint identity and cryptographic state. Decide supported behavior and recovery rules before implementing persistence.
- Cloud PostgreSQL and initial self-hosted SQLite are selected. Concrete object-store drivers, replication/durability requirements, and the endpoint database remain open.

### Configurable Persistence — Initial Backends Selected

- Abstract persistence behind operations with defined behavior, and select a supported driver using configuration. A database URL alone does not make different engines interchangeable.
- Initial drivers: PostgreSQL 18 for the cloud and SQLite for a simple single-host self-hosted installation. Their different write-concurrency characteristics must be handled behind the backend boundary. [SQLite transactions](https://www.sqlite.org/lang_transaction.html)
- Express domain guarantees such as reserving owner capacity, consuming a one-time prekey, committing a frame, recording a durable acknowledgment, and cancelling a transfer. Each driver must preserve their atomicity/idempotency guarantees using its own transaction/locking model.
- Keep database migrations and engine-specific concurrency handling behind the backend boundary. Future implementation verification must exercise the same behavioral contracts across supported drivers.
- Keep large-ciphertext storage behind a separate content-store interface supporting local files or object storage. The metadata database indexes these payloads.
- Prefer a backend identifier and opaque object key/relative storage key over a client-supplied absolute filesystem path or an expiring signed URL. Records also need owner/transfer association, sequence or range, encrypted byte size, lifecycle status, and expiry; exact schema is pending.
- Payload commit and metadata commit are not automatically one transaction. Coordinate quota reservation, payload write, committed metadata, and retry/orphan cleanup. Cancellation must prevent late writes from making cleaned-up payload visible again.
- A modular backend does not promise support for every SQL/NoSQL engine. Document supported drivers and require necessary durability, consistency, and conditional-update capabilities.

### Open Questions

Priority questions for the next discussion:

1. Q01 — For the selected API plus CLI and TCP-forwarding scope, which languages/platforms come first, and should the programming API be an in-process library, local agent API, or both? What listener, destination, and connection-lifecycle interfaces should the TCP adapter expose?
2. Q02 — What are the default and configurable retention, pause/idle, and per-stream limits for the selected resumable buffered streams? Live-only is not required; interruption/resumption is required.
3. Q03 — For self-hosted installations without user verification, how should administrators group endpoints under a common quota owner? Sender-account quota aggregation in verified deployments is already decided.
4. Q04 — Which initial handoff surfaces are needed for the accepted PAKE text-secret pairing: manual entry, local export/import, or a specific trusted provisioning integration? Email OTP and TOTP are decided alternatives; specify first-use enrollment and recovery, especially for TOTP-only accounts.
5. Q05 — How are endpoints added, revoked, and recovered under an existing verified account? Independent endpoint identities and shared owner quota are already decided; account recovery must not silently reset peer trust.
6. Q06 — Which concrete file/object-store drivers and durability guarantees are needed first? PostgreSQL 18, initial self-hosted SQLite, and external large-ciphertext storage with database indexes are already selected.

Further questions before schema/protocol finalization:

7. Q07 — Retention periods, per-stream limits, chunk/message sizes, local queue bounds, and maximum pause/idle time. Which timeout conditions expire data versus require manual cancellation?
8. Q08 — Are channels strictly one-to-one and persistent? Are groups, multiple consumers, or cross-server federation required?
9. Q09 — Does stream/file access need parallel chunks, out-of-order download, random access, or only sequential consumption? The user has accepted chunk resumption; its detailed granularity remains open.
10. Q10 — Are account recovery, endpoint revocation, key backup, device migration, VM cloning, and restoration of old unread data required? Email account recovery alone must not be assumed to recover E2EE keys.
11. Q11 — What metadata should be hidden, and how long must content remain confidential? Are post-quantum protections a requirement?
12. Q12 — Which self-hosted authentication integrations are needed first, and how should unattended services enroll or renew credentials?
13. Q13 — Expected concurrent endpoints/channels, messages per second, transfer sizes/rates, quotas, maximum offline duration, and single-instance versus highly available deployments?
14. Q14 — What failure must a relay-accepted receipt survive: process restart, whole-machine disk loss, or a larger outage? How should the API expose accepted, durably received, and application-completed milestones?
15. Q15 — For the generic fixed-capacity window, should capacity be reserved in full from the sending owner at creation? What are the record-size limits and exact ciphertext/overhead accounting rules?
16. Q16 — Should the initial window support one writer and multiple independent readers? Should close preserve the final retained range until explicit deletion/expiry, or release the allocation immediately? How should a reader select a restart position after an explicit eviction gap?

Next step: discuss [Plan 00](docs/plans/00-core-contracts-and-technology.md), resolve its blocking choices, and refine the remaining plans one at a time under the root project plan. Existing open questions remain assigned to the relevant plan discussions; creating the plan files does not mean those questions have been resolved.
