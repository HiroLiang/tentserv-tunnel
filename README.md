# tentserv-tunnel

A planned end-to-end encrypted communication and transfer layer for devices, virtual machines, and services. Connect through a default cloud relay or a self-hosted server, using an API or CLI.

The project is currently defining its requirements and architecture. The capabilities below describe the planned behavior, not a completed implementation or finalized command syntax.

## Features and possible uses

| Feature | Planned behavior | Example uses |
| --- | --- | --- |
| **Text** | Send a complete encrypted message; reject the send when storage capacity is insufficient. | Device status, task requests, notifications, and service events. |
| **Chunk** | Transfer a bounded object in resumable parts; pause when full and release server storage after durable receipt. | Files, datasets, backups, recordings, and generated artifacts. |
| **Stream** | Push ordered data with bounded server buffering, backpressure, and recovery from interruptions while the transfer remains valid. | Generated AI output, continuous logs, job progress, and incremental results. |
| **Window** | Open a fixed-capacity buffer and keep pushing data; the retained range advances as older data is evicted. | Recent telemetry, a log tail, recent state observations, or application-managed segments for monitoring and live viewing. |
| **TCP forwarding** | Connect an authorized local port to an authorized service on a paired endpoint through an encrypted channel. | Private web apps, remote model APIs, database tools, SSH/SFTP, and other TCP-based services. |

These are examples of what users can build. The service transports and retains opaque encrypted data; applications own payload formats, interpretation, and business behavior.

## Example: connect to a private service

Suppose a web application on device A listens at `127.0.0.1:3000`:

1. A authorizes that local service for a paired channel.
2. B maps the channel to its own `127.0.0.1:8080` listener.
3. B opens the local address in a browser; the forwarding adapters carry traffic through the encrypted channel to A.

The same pattern can connect an existing client to a remote model API or database service. Keep the target service's own authentication and native TLS/SSH encryption where applicable. The adapters form the tunnel's encryption endpoints; local connections to applications remain part of the endpoint trust boundary.

A brief relay interruption may be recoverable while the local connections remain alive. Replaying transport data cannot automatically recreate an application TCP session that has already ended.

## Example: receive generated output after reconnecting

An application can publish generated output or job progress through a reliable stream. Accepted ciphertext waits on the relay while the recipient is disconnected, subject to the sender's quota and the transfer's retention policy. Once the recipient reconnects, it resumes from durable delivery progress.

This recovers delivery of persisted data. Restarting the underlying computation still requires the application's own saved state.

## Example: keep only a bounded recent window

A producer can open a fixed-capacity window and continuously append records. A consumer follows the retained range; old records are removed as new records need room. If the consumer falls behind that range, it must handle an explicit gap.

An application might use this for a recent log tail, sensor history, or encrypted segments consumed by a monitoring viewer. The service does not parse log lines, sensor values, codecs, or video frames.

A capacity limit is measured in storage, not playback time. An application seeking roughly 30 seconds of recent data must account for its data rate and format; a fixed byte limit alone does not promise a fixed time interval. Window eviction also cannot revoke content an authorized consumer has already saved.

## Pairing and deployment

- Endpoints have independent cryptographic identities. Pairing uses an endpoint-generated one-time secret conveyed through a trusted channel outside the relay's control, with PAKE used to establish the peer relationship.
- Pairing can use terminal text and does not require a QR display. Manual transfer or a trusted handoff integration can supply the secret to the other endpoint.
- Cloud account verification supports email OTP or TOTP as alternatives. Self-hosted deployments may omit account verification or integrate their own system; endpoint pairing and channel authorization remain separate responsibilities.
- Stored outgoing data is charged to the sending account across its endpoints and channels.
- Cloud metadata uses PostgreSQL 18; initial self-hosting support uses SQLite. Large ciphertext resides in local files or object storage, indexed by the metadata database.

The cryptographic libraries, API details, storage drivers, and some lifecycle policies are still being selected.
