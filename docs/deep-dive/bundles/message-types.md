# Control Message Types

Control messages coordinate DTN routing, group management, and delivery confirmation. They are transmitted as bundle payloads with a `msgType` field.

## Message Type Reference

| msgType | Class | Purpose |
|---------|-------|---------|
| 1 | `SubMsg` | Topic subscription management |
| 2 | `HopAck` | Hop custody acknowledgment |
| 3 | `DeliveryReceipt` | E2E receipt (deprecated) |
| 4 | `LinkMetrics` | Routing quality metrics |
| 5 | `Payment` | Micropayment for routing incentives |
| 6 | `GroupInviteMsg` | Encrypted group invitation |
| 7 | `GroupUpdateMsg` | Membership add/remove |
| **8** | **`DeliveryAck`** | **Signed E2E acknowledgment** |
| 9 | `DeliveryCancel` | Signed bundle cancellation |
| 10 | `GroupAnnounceMsg` | Public group discovery |
| 12 | `ConfigUpdate` | Remote configuration |

## Detailed Message Specifications

### SubMsg (msgType=1)

Topic subscription management for groups and channels.

| Field | Size | Description |
|-------|------|-------------|
| topicId | 32 bytes | Topic identifier |
| action | 1 byte | SUBSCRIBE (0) or UNSUBSCRIBE (1) |
| expiresAt | 8 bytes | Optional expiration timestamp |
| timestamp | 8 bytes | Message creation time |

### HopAck (msgType=2)

Confirms custody transfer at each hop.

| Field | Size | Description |
|-------|------|-------------|
| bundleId | 32 bytes | Acknowledged bundle ID |
| fromNode | 32 bytes | Acknowledging node's public key |
| timestamp | 8 bytes | Acknowledgment time |

Used for:
- Spray-and-Wait copy budget management
- Confirming successful hop-by-hop delivery

### DeliveryAck (msgType=8)

**Primary ACK mechanism.** Signed end-to-end acknowledgment from the destination.

| Field | Size | Description |
|-------|------|-------------|
| msgId | 32 bytes | Original bundle ID |
| toUid | 32 bytes | Origin UID |
| fromUid | 32 bytes | Destination UID |
| deliveredAtMs | 8 bytes | Delivery timestamp |
| status | 1 byte | 1 = delivered to app |
| ackType | 1 byte | 1 = RECIPIENT |
| ackH3r5 | 8 bytes | Coarse geo hint (optional) |
| ackH3r7 | 8 bytes | Fine geo hint (optional) |
| hopCount | 4 bytes | Observed hop count (optional) |
| latencyMs | 8 bytes | Delivery latency (optional) |
| transportHint | variable | "nearby" or "nostr" |
| path | variable | Per-hop metadata (optional) |
| sig | 64 bytes | Ed25519 signature |

**Key Properties:**

- **Signed** by destination - proves authentic delivery
- **Routed back** to origin via DTN
- **Improves routing** via ACK path learning

### DeliveryCancel (msgType=9)

Signed cancellation instruction.

| Field | Size | Description |
|-------|------|-------------|
| msgId | 32 bytes | Bundle to cancel |
| reason | 1 byte | Cancellation reason |
| issuedByUid | 32 bytes | Issuer UID |
| sig | 64 bytes | Ed25519 signature |

| Reason | Value | Description |
|--------|-------|-------------|
| ACKED | 1 | Already delivered, clean up copies |
| EXPIRED | 2 | TTL exceeded |
| RECALLED | 3 | Sender cancelled |

### GroupInviteMsg (msgType=6)

Encrypted group invitation for private groups.

| Field | Size | Description |
|-------|------|-------------|
| encryptedPayload | variable | ECIES-encrypted invite |
| ephemeralPubKey | 32 bytes | X25519 ephemeral key |
| nonce | 24 bytes | XChaCha20-Poly1305 nonce |

**Encryption:** ECIES with XChaCha20-Poly1305

The encrypted payload contains:
- Group ID
- Group name
- Group symmetric key (for message encryption)
- Admin public key

### GroupUpdateMsg (msgType=7)

Membership changes for existing groups.

| Field | Size | Description |
|-------|------|-------------|
| encryptedPayload | variable | AES-GCM encrypted update |
| nonce | 12 bytes | AES-GCM nonce |

**Encryption:** AES-256-GCM with group symmetric key

The encrypted payload contains:
- Action: ADD_MEMBER or REMOVE_MEMBER
- Target user UID
- Admin signature

### GroupAnnounceMsg (msgType=10)

Public group discovery broadcast.

| Field | Size | Description |
|-------|------|-------------|
| groupIdString | variable | Group identifier (e.g., "group:hiking-club") |
| shortId | 10 chars | 10-character hash |
| displayName | variable | Human-readable name |
| description | variable | Optional description |
| creatorUidHex | 64 chars | Creator's UID (hex) |
| createdAt | 8 bytes | Creation timestamp |
| creatorSig | 64 bytes | Ed25519 signature |

**Key Properties:**

- Plaintext (not encrypted)
- Signed by group creator
- Broadcast to mesh group topic
- Only for PUBLIC groups

### LinkMetrics (msgType=4)

Network quality metrics for intelligent routing.

| Field | Size | Description |
|-------|------|-------------|
| clusterTag | 32 bytes | MycTagStar |
| gatewayScore | 4 bytes | 0.0-1.0 score |
| queueLength | 4 bytes | Outbound queue size |
| deltaIn | 4 bytes | Bundles received |
| deltaOut | 4 bytes | Bundles forwarded |

### Payment (msgType=5)

Micropayment for routing incentives (future use).

| Field | Size | Description |
|-------|------|-------------|
| payer | 32 bytes | Payer public key |
| payee | 32 bytes | Payee public key |
| amount | 8 bytes | Payment amount |
| nonce | 8 bytes | Replay prevention |
| sig | 64 bytes | Payer signature |

### ConfigUpdate (msgType=12)

Remote configuration from trusted manager.

| Field | Size | Description |
|-------|------|-------------|
| version | 8 bytes | Config version |
| issuedAtMs | 8 bytes | Issue timestamp |
| expiresAtMs | 8 bytes | Expiration (optional) |
| flags | variable | Key-value pairs |
| issuerKey | 32 bytes | Trusted key |
| signature | 64 bytes | Ed25519 signature |

## Serialization

All control messages use CBOR (Concise Binary Object Representation) serialization for efficient binary encoding.

## Routing Priority

Control messages have different priorities:

| Message Type | Priority | Reason |
|--------------|----------|--------|
| DeliveryAck | CRITICAL (0) | Enables cleanup |
| DeliveryCancel | CRITICAL (0) | Stops wasted effort |
| HopAck | CRITICAL (0) | Copy budget tracking |
| GroupInvite | NORMAL (1) | User action |
| GroupUpdate | NORMAL (1) | Membership sync |
| LinkMetrics | BULK (2) | Background telemetry |

---

**Next:** [Bundle Lifecycle](lifecycle.md) | [Encryption](encryption.md)
