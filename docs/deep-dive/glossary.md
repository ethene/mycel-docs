# Glossary

Technical terms used throughout the Mycel documentation. User-level terms are marked with **(User)**.

---

## A

### ACK (Acknowledgment)
Confirmation that a message was delivered. See [DeliveryAck](bundles/message-types.md#deliveryack-msgtype8).

### AES-256-GCM
Symmetric encryption algorithm used for group messages and DM content encryption.

## B

### Bundle
Self-contained message unit in DTN. Contains header (routing metadata) and payload (encrypted content). Think of it as a sealed envelope with address labels. See [Bundle Structure](bundles/structure.md).

### Bundle Status
Lifecycle state of a bundle: NEW, RECEIVED, RECEIVED_FORWARDABLE, FWD, DEL, EXP, ERR, CANCELLED. See [Bundle Lifecycle](bundles/lifecycle.md).

## C

### CBOR
Concise Binary Object Representation. Binary serialization format used for bundles and control messages.

### Control Message
System message for routing, subscriptions, and network coordination. Types 1-12. See [Message Types](bundles/message-types.md).

### Copy Budget
Spray-and-Wait parameter limiting message replication. Decrements on each forward.

### CRDT
Conflict-free Replicated Data Type. Used for group membership synchronization. See [Private Groups](groups/private-groups.md).

## D

### DeliveryAck
Signed end-to-end acknowledgment from recipient (msgType=8). Primary ACK mechanism.

### DeviceId
Ed25519 public key identifying a device. 32 bytes.

### DRAIN
Session state: connected, transfer-ready, queues empty, idle.

### Delivery **(User)**
When your message reaches its intended recipient. In Mycel, this can take seconds (nearby) or days (multi-hop across the mesh).

### DTN
Delay-Tolerant Networking. Architecture for communication without continuous connectivity. The key insight: embrace delays rather than require always-on connections.

## E

### ECIES
Elliptic Curve Integrated Encryption Scheme. Used for DM encryption: X25519 ECDH + HKDF + AES-256-GCM.

### Ed25519
Elliptic curve signature algorithm. Used for device identity and message signing.

## F

### Forward (FWD)
Bundle status indicating active forwarding in progress.

## G

### Gift-Wrap
NIP-17 Nostr encryption that hides sender/recipient from relays. Double encryption with random author.

### Group Key
32-byte AES-256 symmetric key shared by all group members.

### GroupInviteMsg
Control message (msgType=6) for encrypted group invitations.

## H

### H3
Uber's hexagonal hierarchical spatial index. Used for geographic routing.

### HKDF
HMAC-based Key Derivation Function. Derives AES key from ECDH shared secret.

### HopAck
Control message (msgType=2) confirming custody transfer at each hop.

## I

### Interest Filter
Bloom filter containing topics a device is interested in. Used for routing.

### IDLE
Session state: disconnected, no active connection.

## L

### L2CAP
Bluetooth Logical Link Control and Adaptation Protocol. Used for BLE data transfer.

## M

### Mesh **(User)**
The network of Mycel devices that relay messages for each other. Everyone running Mycel is part of the mesh. More people = better coverage = faster delivery.

### MeshService
Android foreground service managing DTN operations.

### MycTag
Human-readable identity string. Format: `myc:<10-char-base32>`.

### MycTagStar
Cluster identity derived from member public keys.

## N

### Nearby Connections
Google Play Services API for local device discovery and communication via BLE/WiFi.

### NIP
Nostr Implementation Possibility. Protocol extension for Nostr.

### NIP-17
Gift-wrap encryption for private DMs on Nostr relays.

### NIP-44
Versioned encryption for Nostr event content.

### Nostr
Protocol for decentralized communication via relay servers.

## P

### P-value
PRoPHET delivery predictability probability (0.0-1.0).

### PRoPHET
Probabilistic Routing Protocol using History of Encounters and Transitivity. See [PRoPHET Algorithm](routing/prophet.md).

## R

### Relay **(User)**
Any device that carries your message toward its destination. Every Mycel user's phone acts as a relay for other people's messages. You help others; they help you.

### Relay (Nostr)
WebSocket server that stores and forwards Nostr events.

### Route Path
List of clusters a bundle has traversed. Max 8 hops.

## S

### Session State
Connection lifecycle state: IDLE, DISCOVERY, CONNECTING, SESSION_OPEN, SYNC, XFER, DRAIN, CLOSING. See [Nearby Transport](transports/nearby.md).

### Spray-and-Wait
Routing algorithm limiting message copies to prevent flooding.

### Store-and-Forward
DTN principle where nodes store messages locally until forwarding opportunity.

## T

### Topic
Identifier for group or channel. 32-byte hash used for interest-based routing.

### TTL
Time-to-Live. Duration after which a bundle expires.

### Transport
Physical delivery mechanism: Nearby Connections or Nostr.

## U

### UID
User ID. SHA256 hash of device public key. 32 bytes.

## W

### Watchdog
Background process monitoring Nearby connection health.

## X

### X25519
Elliptic curve Diffie-Hellman for key exchange. Derived from Ed25519 keys.

### XFER
Session state: connected, actively transferring data.

### XChaCha20-Poly1305
Authenticated encryption used for group invites. 24-byte nonce variant.

---

## Quick Reference

| Term | Size | Algorithm |
|------|------|-----------|
| Device Key | 32 bytes | Ed25519 public |
| Private Key | 64 bytes | Ed25519 (seed + public) |
| UID | 32 bytes | SHA256(pubKey) |
| MycTag | 10 chars | base32(UID[:6]) |
| Bundle ID | 32 bytes | SHA256(header + payload) |
| Signature | 64 bytes | Ed25519 |
| Group Key | 32 bytes | AES-256 |
| Nonce (GCM) | 12 bytes | Random |
| Nonce (XChaCha) | 24 bytes | Random |

---

**See also:** [Index](index.md) | [Architecture](architecture/overview.md)
