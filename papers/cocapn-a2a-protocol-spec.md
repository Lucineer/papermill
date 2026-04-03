# Cocapn A2A: The Agent-to-Agent Protocol for Repo-Native Intelligence

**Superinstance & Lucineer (DiGennaro et al.)**

*Draft — April 2026*

---

## Abstract

Cocapn vessels—autonomous agents that live inside git repositories—require a structured way to discover, authenticate, and communicate with one another. Existing agent communication protocols assume centralized broker architectures or tool-invocation models that do not fit the repo-native paradigm, where each vessel's identity, state, and knowledge graph are co-located with its source code. This paper defines the Cocapn A2A Protocol, a lightweight HTTP-based protocol for vessel-to-vessel communication covering message types, fleet discovery, authentication, knowledge transfer, conflict resolution, privacy, and transport security.

## 1. Motivation

The Cocapn runtime deploys autonomous agents—*vessels*—that treat a git repository as their canonical home. Each vessel owns a knowledge graph, a set of behavioral patterns, and an HTTP endpoint. When multiple vessels operate within the same fleet or across fleets, they need to:

- **Discover** each other without manual configuration.
- **Authenticate** peers based on cryptographic proof of repository ownership.
- **Exchange knowledge**—query responses, learned patterns, locked tiles—without leaking private reasoning.
- **Resolve conflicts** when shared knowledge graphs diverge.

Today, inter-vessel communication relies on ad-hoc HTTP calls with no standard envelope, no replay protection, and no discovery mechanism. This does not scale beyond two vessels on a whiteboard. The Cocapn A2A Protocol provides the missing contract.

## 2. Protocol Fundamentals

### 2.1 Design Principles

1. **Repo-native identity.** A vessel's identity is derived from its repository URL and a deploy-time signature, not from a central registry or human-assigned handle.
2. **Minimal transport.** HTTP/1.1 or HTTP/2 over TLS. No WebSockets, no gRPC, no message queues. Keep it debuggable with `curl`.
3. **Stateless messages, stateful vessels.** Each message is self-contained. Vessels maintain their own state; the protocol does not define shared sessions.
4. **Optimistic cooperation.** Vessels assume goodwill; security layers detect and reject bad actors.

### 2.2 Message Envelope

Every Cocapn A2A message is a JSON object over HTTP POST:

```jsonc
{
  "protocol": "cocapn-a2a",
  "version": "1.0",
  "type": "ping | query | broadcast | sync | transfer | heartbeat | handshake",
  "id": "uuid-v4",
  "from": {
    "vessel": "https://github.com/org/repo",
    "fingerprint": "sha256:abc123...",
    "fleet": "fleet-id or null"
  },
  "to": "vessel-url | fleet:broadcast | null",
  "timestamp": 1712000000.000,        // Unix epoch, seconds, float
  "ttl": 30,                          // seconds before stale
  "payload": { },                     // type-specific body
  "signature": "ed25519:base64..."    // signed over canonical JSON of everything above
}
```

The `signature` field covers all fields except itself, serialized as deterministic JSON (sorted keys, no whitespace). Verification uses the sender's Ed25519 public key, which is published at `{vessel-origin}/.well-known/cocapn-vessel.json`.

### 2.3 Transport

All communication uses HTTPS. Vessels MUST reject HTTP connections. The default port is 443; vessels running on non-standard ports include the port in their advertised origin.

Request and response bodies are `application/json`. Responses follow the same envelope structure with `type` set to `{original-type}.ack` or `{original-type}.error`.

## 3. Message Types

### 3.1 Ping

```json
{ "type": "ping", "payload": {} }
```

Health check. Responds with a pong containing the vessel's current protocol version and uptime.

### 3.2 Query

```json
{
  "type": "query",
  "payload": {
    "intent": "ask_capability | ask_knowledge | ask_status",
    "params": { "question": "..." }
  }
}
```

Directed question to a specific vessel. The `intent` field scopes what the responder may return. Vessels MUST NOT return private reasoning chains unless the query type explicitly requests them and the requester is authorized.

### 3.3 Broadcast

```json
{
  "type": "broadcast",
  "payload": {
    "topic": "fleet_status | alert | announcement",
    "body": { }
  }
}
```

Sent to all vessels in a fleet. Fleet manifests define delivery; the sender does not enumerate recipients.

### 3.4 Sync

```json
{
  "type": "sync",
  "payload": {
    "graph_delta": "crdt-patch-serialized",
    "base_vector": [1, 0, 3, ...]
  }
}
```

Propagates knowledge-graph changes using CRDT patches (see §7). The `base_vector` allows the receiver to determine if it has the necessary causal history to apply the patch.

### 3.5 Transfer

```json
{
  "type": "transfer",
  "payload": {
    "artifact_type": "tile | pattern | locked_response",
    "artifact_id": "sha256:...",
    "artifact": { }  // or a signed URL for large artifacts
  }
}
```

Sends a discrete knowledge artifact. For artifacts >1 MB, the payload contains a signed URL rather than inline data.

### 3.6 Heartbeat

```json
{
  "type": "heartbeat",
  "payload": {
    "status": "alive | degrading | draining",
    "load": 0.72
  }
}
```

Periodic liveness signal. Fleets use heartbeats for leader election and load balancing. Default interval: 60 seconds.

### 3.7 Handshake

```json
{
  "type": "handshake",
  "payload": {
    "capabilities": ["query", "sync", "transfer"],
    "fleet_id": "optional-fleet-join-request",
    "auth_challenge": "random-nonce"
  }
}
```

Initial exchange when two vessels meet. Both sides send handshakes. The challenge/nonce mechanism binds the handshake to the specific session, preventing replay.

## 4. Discovery

### 4.1 Fleet Manifest

Each fleet maintains a manifest stored in a shared KV store (Cocapn's built-in key-value layer). The manifest is a JSON document listing all active vessels:

```json
{
  "fleet_id": "prod-west",
  "vessels": [
    {
      "url": "https://vessel-a.cocapn.dev",
      "repo": "https://github.com/org/repo-a",
      "fingerprint": "sha256:...",
      "last_heartbeat": 1712000060,
      "capabilities": ["query", "sync", "transfer"]
    }
  ],
  "updated_at": 1712000100
}
```

Vessels register by writing their entry on startup and update `last_heartbeat` with each heartbeat cycle. Entries older than `3 × heartbeat_interval` are considered stale and pruned.

### 4.2 Gossip Protocol

For deployments without a shared KV store (e.g., air-gapped fleets), vessels use a gossip protocol. Each vessel maintains a partial view of the fleet and periodically shares its view with a random peer. Gossip messages use the `sync` message type with a `gossip_view` payload. Convergence is bounded by `O(log N)` rounds where N is fleet size.

### 4.3 Well-Known Endpoint

Every vessel publishes its metadata at `/.well-known/cocapn-vessel.json`:

```json
{
  "protocol_version": "1.0",
  "repo": "https://github.com/org/repo",
  "public_key": "ed25519:base64...",
  "capabilities": ["query", "sync", "transfer"],
  "fleet_id": "prod-west",
  "endpoints": {
    "a2a": "/api/a2a"
  }
}
```

This enables bootstrapping: given a vessel URL, any peer can discover its identity, capabilities, and fleet membership in a single request.

## 5. Authentication

Authentication in Cocapn A2A is based on **repo-derived identity**:

1. **Identity derivation.** A vessel's identity is the hash of its repository URL (`sha256(repo-url)`). This is not a secret—it's a stable identifier.
2. **Deploy signature.** When a vessel is deployed, the deployer signs the vessel identity with an Ed25519 key pair. The public key is committed to the repository (e.g., `.cocapn/vessel.pub`).
3. **Message signing.** Every message is signed with the vessel's private key. Receivers verify the signature against the published public key.
4. **Well-known verification.** Before accepting any message, a vessel fetches the sender's `/.well-known/cocapn-vessel.json`, extracts the public key, and verifies the message signature. Results are cached for `heartbeat_interval`.

This model provides **repudiable authentication**: anyone can verify that a message came from a vessel, but the vessel's private key never leaves its runtime. Compromise of a vessel revokes trust for that specific repo, not for the entire fleet.

## 6. Knowledge Transfer

Vessels exchange three categories of knowledge artifacts:

### 6.1 Patterns

Reusable behavioral templates—e.g., "how to respond to code review requests." Patterns are versioned and carry a semantic hash for deduplication.

### 6.2 Tiles

Composable knowledge units representing a specific domain or task. Tiles are the currency of vessel specialization. Transfer of tiles between vessels enables cross-pollination without full graph sync.

### 6.3 Locked Responses

Pre-computed, immutable responses to frequently asked queries. Locked responses reduce compute load on the fleet by allowing vessels to serve cached answers. They carry an expiry and a freshness hash.

### 6.4 Transfer Flow

1. Requester sends a `query` with `intent: "ask_knowledge"`.
2. Responder replies with available artifact metadata (type, hash, size).
3. Requester sends a `transfer` request for desired artifacts.
4. Responder sends the artifact(s) as `transfer` messages.

## 7. Conflict Resolution

When multiple vessels write to overlapping regions of a shared knowledge graph, conflicts are inevitable. Cocapn A2A uses **Conflict-free Replicated Data Types (CRDTs)** for merge resolution.

### 7.1 Data Structures

- **Knowledge entries** use LWW-Register (Last-Writer-Wins) with wall-clock timestamps broken by vessel fingerprint as a tiebreaker.
- **Graph edges** use OR-Set (Observed-Remove Set) to handle concurrent additions and removals.
- **Tile versions** use a version vector: each vessel maintains a monotonic counter per graph partition.

### 7.2 Merge Strategy

When a `sync` message arrives:

1. The receiver compares the sender's version vector against its own.
2. If the sender is behind, the receiver sends a reverse sync with missing patches.
3. If the sender is ahead, the receiver applies incoming patches using CRDT merge rules.
4. If vectors diverge (both have unique entries), both sides apply the other's patches. LWW-Registers resolve any key-level conflicts deterministically.

### 7.3 Garbage Collection

Tombstoned entries (removed via OR-Set semantics) are retained for `2 × max_clock_skew` (default: 10 minutes) before garbage collection. This window ensures all vessels have seen the removal before it is permanently deleted.

## 8. Privacy Model

### 8.1 Information Tiers

| Tier | Visibility | Examples |
|------|-----------|---------|
| **Public** | Any vessel, any fleet | Capabilities, protocol version, uptime |
| **Fleet** | Same fleet only | Heartbeat status, fleet-wide broadcasts |
| **Private** | Origin vessel only | Reasoning chains, internal state, user data |
| **Shared** | Explicitly granted vessels | Knowledge tiles, patterns, locked responses |

### 8.2 Consent Model

- Vessels default to **private**. Sharing requires an explicit `grant` in the vessel's configuration.
- Cross-fleet queries require bilateral consent: both the querying vessel and the responding vessel must have cross-fleet permissions enabled.
- A vessel can revoke sharing at any time. Revocation takes effect within one heartbeat cycle.

### 8.3 Query Scoping

Query messages include an `intent` field that restricts what the responder may return. A `query` with `intent: "ask_capability"` MUST NOT return knowledge artifacts. This prevents scope creep even between trusting vessels.

## 9. Transport Security

### 9.1 TLS Requirements

- TLS 1.2 or higher. TLS 1.3 preferred.
- Certificate validation is mandatory. Self-signed certificates are acceptable only in development (`COCAPN_DEV=true`).
- Certificate pinning is optional but recommended for static fleet topologies.

### 9.2 Request Signing

Beyond the message-level Ed25519 signature, Cocapn A2A adds HTTP-level signing:

1. The sender constructs a canonical string: `{method}\n{url-path}\n{timestamp}\n{body-hash}`.
2. This string is signed with the vessel's Ed25519 key and included in the `X-Cocapn-Signature` header.
3. The receiver verifies the header signature independently of the message body signature.

This two-layer signing prevents both message tampering and HTTP-level attacks (e.g., path manipulation, body injection).

### 9.3 Replay Prevention

Every message includes a monotonically increasing sequence number per sender-receiver pair (the `id` field is a UUIDv4, but vessels track seen IDs). Messages with previously-seen IDs from the same sender are rejected. The seen-ID cache retains entries for `2 × ttl` of the longest TTL received from that sender.

## 10. Versioning

### 10.1 Protocol Version

The `protocol` and `version` fields in the message envelope identify the protocol version. Current version: `1.0`.

### 10.2 Backward Compatibility

- **Minor versions** (1.x) are backward-compatible. New message types and payload fields may be added; existing ones are unchanged.
- **Major versions** (2.0+) MAY break compatibility. Vessels MUST negotiate version during handshake.
- Vessels that receive a message with an unsupported version respond with `{ "type": "handshake.error", "payload": { "supported_versions": ["1.0", "1.1"] } }`.

### 10.3 Feature Detection

Handshake `capabilities` lists enable feature negotiation. A vessel that does not advertise `sync` capability MUST NOT receive sync messages. Senders are responsible for checking capabilities before sending typed messages.

## 11. Implementation in ZeroClaw

The reference implementation lives in ZeroClaw's `fleet.ts` class. Key design decisions:

- **Fleet class** maintains the local vessel's identity, peer list, and message handlers.
- **Message dispatch** uses a typed handler map: `handlers.set("ping", handlePing)`.
- **Discovery polling** reads the fleet manifest from KV every 30 seconds (configurable).
- **Sync scheduler** batches graph deltas and flushes every 10 seconds or when the delta exceeds 64 KB.
- **Signature verification** is lazy: the first message from a new sender triggers a `/.well-known` fetch; subsequent messages use the cached public key.
- **Gossip** is implemented as a background interval that selects a random peer and exchanges partial views.

The `fleet.ts` implementation is approximately 400 lines of TypeScript and serves as the authoritative source of truth for protocol behavior. Any divergence between this specification and the code should be resolved in favor of the code, with a subsequent spec revision.

## 12. Comparison to Existing Protocols

| Aspect | Cocapn A2A | Google A2A | Anthropic MCP | ActivityPub |
|--------|-----------|------------|---------------|-------------|
| **Identity model** | Repo-derived, self-signed | Agent ID + remote auth | Client-server, tool-oriented | Actor URL + WebFinger |
| **Transport** | HTTP/REST (JSON) | HTTP/REST (JSON-RPC) | stdio or HTTP (JSON-RPC) | HTTP + LD Signatures |
| **Discovery** | Fleet manifest + gossip | Agent registry | Hardcoded server | WebFinger + inbox |
| **State model** | CRDT knowledge graphs | Task-based state machine | Stateless tool calls | Activity streams |
| **Privacy** | Tiered, consent-based | Agent-scoped | Server-scoped | Federated, per-actor |
| **Conflict resolution** | CRDT merge | Single-writer (agent) | N/A | No built-in CRDT |
| **Primary use case** | Repo-native agent fleets | Multi-agent task delegation | LLM tool integration | Social federation |
| **Trust model** | Deployer-signed, no CA | OAuth2, API keys | Server trust | WebFinger + signatures |

**Key differentiators:**

- **Google A2A** focuses on task delegation between agents managed by a platform. Cocapn A2A assumes no platform—vessels are self-hosted, repo-native, and self-identifying.
- **Anthropic MCP** is a tool-invocation protocol, not an agent-to-agent protocol. It defines how a client calls tools on a server, not how two autonomous agents collaborate.
- **ActivityPub** is the closest analog in spirit (federated, self-identifying actors), but targets social content rather than autonomous knowledge exchange. Its activity-stream model lacks the CRDT-based conflict resolution and tiered privacy that Cocapn requires.

## 13. Future Work

- **Streaming sync.** Large graph deltas benefit from chunked transfer. A `sync.stream` message type is under consideration.
- **Attestation.** Integrating with Sigstore or similar transparency logs for deploy-time key verification.
- **Cross-protocol bridges.** An ActivityPub-to-Cocapn adapter could enable social-agent hybrid systems.
- **Formal verification.** The CRDT merge rules should be formally verified (e.g., via TLA+) to guarantee convergence under all interleaving.

## 14. Conclusion

The Cocapn A2A Protocol provides a minimal, secure, and privacy-aware contract for repo-native agents to discover, authenticate, and cooperate. By grounding identity in repository provenance, using CRDTs for conflict resolution, and enforcing tiered privacy with consent, it enables autonomous fleets that scale without centralized control. The protocol is intentionally simple—HTTP, JSON, Ed25519—because the complexity belongs in the agents, not in the plumbing.

---

*DiGennaro, C., et al. "Cocapn A2A: The Agent-to-Agent Protocol for Repo-Native Intelligence." 2026.*
