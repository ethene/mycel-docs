# Control Message Types

Control messages coordinate DTN routing, group management, and delivery confirmation. They are transmitted as bundle payloads with a `msgType` field.

**Source:** `core/dtn/src/main/kotlin/com/meshlablite/core/dtn/ControlMsg.kt`

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

```kotlin
data class SubMsg(
    val topicId: ByteArray,      // Topic/channel/group ID (32 bytes)
    val action: SubAction,       // SUBSCRIBE or UNSUBSCRIBE
    val expiresAt: Long? = null, // Optional expiration
    val timestamp: Long
)
```

| Field | Size | Description |
|-------|------|-------------|
| topicId | 32 bytes | Topic identifier |
| action | 1 byte | SUBSCRIBE (0) or UNSUBSCRIBE (1) |
| expiresAt | 8 bytes | Optional expiration timestamp |

### HopAck (msgType=2)

Confirms custody transfer at each hop.

```kotlin
data class HopAck(
    val bundleId: ByteArray,  // Acknowledged bundle (32 bytes)
    val fromNode: ByteArray,  // Acknowledging node's pubkey (32 bytes)
    val timestamp: Long
)
```

Used for:
- Spray-and-Wait copy budget management
- Confirming successful hop-by-hop delivery

### DeliveryAck (msgType=8)

**Primary ACK mechanism.** Signed end-to-end acknowledgment from the destination.

```kotlin
data class DeliveryAck(
    val msgId: ByteArray,           // Original bundle ID (32 bytes)
    val toUid: ByteArray,           // Origin UID (32 bytes)
    val fromUid: ByteArray,         // Destination UID (32 bytes)
    val deliveredAtMs: Long,        // Delivery timestamp
    val status: Byte = 1,           // 1 = delivered to app
    val ackType: Byte = 1,          // 1 = RECIPIENT
    val ackH3r5: Long? = null,      // Coarse geo hint
    val ackH3r7: Long? = null,      // Fine geo hint
    val hopCount: Int? = null,      // Observed hop count
    val latencyMs: Long? = null,    // Delivery latency
    val transportHint: String?,     // "nearby" or "nostr"
    val path: List<DeliveryPathHop>?, // Per-hop metadata
    val sig: ByteArray              // Ed25519 signature (64 bytes)
)
```

**Key Properties:**
- **Signed** by destination - proves authentic delivery
- **Routed back** to origin via DTN
- **Improves routing** via ACK path learning

### DeliveryCancel (msgType=9)

Signed cancellation instruction.

```kotlin
data class DeliveryCancel(
    val msgId: ByteArray,       // Bundle to cancel (32 bytes)
    val reason: Byte,           // 1=ACKED, 2=EXPIRED, 3=RECALLED
    val issuedByUid: ByteArray, // Issuer UID (32 bytes)
    val sig: ByteArray          // Ed25519 signature (64 bytes)
)
```

| Reason | Value | Description |
|--------|-------|-------------|
| ACKED | 1 | Already delivered, clean up copies |
| EXPIRED | 2 | TTL exceeded |
| RECALLED | 3 | Sender cancelled |

### GroupInviteMsg (msgType=6)

Encrypted group invitation for private groups.

```kotlin
data class GroupInviteMsg(
    val encryptedPayload: ByteArray, // ECIES-encrypted invite
    val ephemeralPubKey: ByteArray,  // X25519 ephemeral (32 bytes)
    val nonce: ByteArray             // XChaCha20-Poly1305 nonce (24 bytes)
)
```

**Encryption:** ECIES with XChaCha20-Poly1305

The encrypted payload contains:
- Group ID
- Group name
- Group symmetric key (for message encryption)
- Admin public key

### GroupUpdateMsg (msgType=7)

Membership changes for existing groups.

```kotlin
data class GroupUpdateMsg(
    val encryptedPayload: ByteArray, // AES-GCM encrypted update
    val nonce: ByteArray             // AES-GCM nonce (12 bytes)
)
```

**Encryption:** AES-256-GCM with group symmetric key

The encrypted payload contains:
- Action: ADD_MEMBER or REMOVE_MEMBER
- Target user UID
- Admin signature

### GroupAnnounceMsg (msgType=10)

Public group discovery broadcast.

```kotlin
data class GroupAnnounceMsg(
    val groupIdString: String,    // "group:hiking-club"
    val shortId: String,          // 10-char hash
    val displayName: String,      // Human-readable name
    val description: String?,     // Optional description
    val creatorUidHex: String,    // Creator's UID (64 hex chars)
    val createdAt: Long,          // Creation timestamp
    val creatorSig: ByteArray     // Ed25519 signature (64 bytes)
)
```

**Key Properties:**
- Plaintext (not encrypted)
- Signed by group creator
- Broadcast to mesh group topic
- Only for PUBLIC groups

### LinkMetrics (msgType=4)

Network quality metrics for intelligent routing.

```kotlin
data class LinkMetrics(
    val clusterTag: ByteArray, // MycTagStar (32 bytes)
    val gatewayScore: Float,   // 0.0-1.0
    val queueLength: Int,      // Outbound queue size
    val deltaIn: Int,          // Bundles received
    val deltaOut: Int          // Bundles forwarded
)
```

### Payment (msgType=5)

Micropayment for routing incentives (future use).

```kotlin
data class Payment(
    val payer: ByteArray,   // Payer pubkey (32 bytes)
    val payee: ByteArray,   // Payee pubkey (32 bytes)
    val amount: Long,       // Payment amount
    val nonce: Long,        // Replay prevention
    val sig: ByteArray      // Payer signature (64 bytes)
)
```

### ConfigUpdate (msgType=12)

Remote configuration from trusted manager.

```kotlin
data class ConfigUpdate(
    val version: Long,
    val issuedAtMs: Long,
    val expiresAtMs: Long?,
    val flags: Map<String, String>,
    val issuerKey: ByteArray,  // Trusted key (32 bytes)
    val signature: ByteArray   // Ed25519 signature (64 bytes)
)
```

## Serialization

All control messages use CBOR serialization:

```kotlin
// Encode
val bytes = controlMsg.toCbor()

// Decode
val msg = ControlMsg.fromCbor(bytes)
```

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

## Source Files

| File | Purpose | Lines |
|------|---------|-------|
| `ControlMsg.kt` | All control message definitions | Full file |
| `BundleRepository.kt` | Control message handling | 1580-1700 |
| `AckPathLearner.kt` | DeliveryAck processing | 45-120 |

---

**Next:** [Bundle Lifecycle](lifecycle.md) | [Encryption](encryption.md)
