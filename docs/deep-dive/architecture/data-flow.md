# End-to-End Message Flow

This page shows how messages flow through Mycel from sender to recipient.

## DM Delivery Flow

```mermaid
sequenceDiagram
    participant Sender as Sender App
    participant SDTN as Sender DTN
    participant ST as Sender Transport
    participant Relay as Nearby/Nostr
    participant RT as Recipient Transport
    participant RDTN as Recipient DTN
    participant Recipient as Recipient App

    Sender->>SDTN: Create message
    SDTN->>SDTN: Encrypt (ECIES)
    SDTN->>SDTN: Sign (Ed25519)
    SDTN->>SDTN: Store bundle (NEW)

    loop Routing attempts
        SDTN->>ST: Forward bundle
        ST->>Relay: Transmit
        Relay->>RT: Receive
        RT->>RDTN: Deliver
    end

    RDTN->>RDTN: Verify signature
    RDTN->>RDTN: Decrypt payload
    RDTN->>Recipient: Display message

    RDTN->>ST: Send DeliveryAck
    ST->>SDTN: Receive ACK
    SDTN->>Sender: Update status (DEL)
```

## Layer Responsibilities

| Layer | Sender Side | Recipient Side |
|-------|-------------|----------------|
| **App** | User types message | Display in conversation |
| **DTN** | Encrypt, sign, store | Verify, decrypt, store |
| **Transport** | Select path, transmit | Receive, forward up |

## Detailed Steps

### 1. Message Creation

```
App Layer:
  User → ChatViewModel → DmUseCase

DTN Layer:
  BundleRepository.createDmBundle()
    1. Generate bundle ID
    2. Encrypt payload (DmCrypto)
    3. Sign header (DeviceKeys)
    4. Store with status=NEW
```

### 2. Routing Decision

```mermaid
flowchart TB
    START([Bundle created]) --> CHECK{Transport available?}
    CHECK -->|Nearby peer| NEARBY[Send via Nearby]
    CHECK -->|Nostr relay| NOSTR[Send via Nostr]
    CHECK -->|Neither| WAIT[Wait in queue]

    NEARBY --> SENT{Transmitted?}
    NOSTR --> SENT

    SENT -->|Yes| ACK_WAIT[Wait for ACK]
    SENT -->|No| RETRY[Retry later]

    ACK_WAIT -->|ACK received| DONE([Delivery confirmed])
    ACK_WAIT -->|Timeout| RETRY

    WAIT --> CHECK
    RETRY --> CHECK
```

### 3. Transport Selection

The routing facade evaluates all available transports:

| Transport | When Used | Priority |
|-----------|-----------|----------|
| Nearby | Peer is connected (XFER/DRAIN state) | High |
| Nostr | Relay is connected, recipient online | Medium |
| Store | No transport available | Fallback |

### 4. Bundle Transmission

**Via Nearby:**
```
SessionRegistry → check XFER/DRAIN state
OutgoingWorker → serialize bundle
NearbyConnections → send payload
WireProtocol → frame with length prefix
```

**Via Nostr:**
```
RelayPool → get connected relays
GiftWrap → encrypt for relay blindness
NostrEvent → create wrapped event
RelayConnection → publish to relays
```

### 5. Bundle Reception

```mermaid
sequenceDiagram
    participant T as Transport
    participant P as PayloadParser
    participant R as BundleRepository
    participant N as NotificationManager

    T->>P: Raw bytes received
    P->>P: Deserialize CBOR
    P->>P: Verify signature
    P->>R: Check duplicate (by ID)

    alt New bundle
        R->>R: Store bundle
        R->>R: Check if for me
        alt Destination is me
            R->>R: Decrypt payload
            R->>N: Notify user
        else Destination is other
            R->>R: Mark FORWARDABLE
        end
    else Duplicate
        R->>R: Drop bundle
    end
```

### 6. Delivery Confirmation

```mermaid
sequenceDiagram
    participant R as Recipient
    participant RDTN as Recipient DTN
    participant T as Transport
    participant SDTN as Sender DTN
    participant S as Sender

    R->>RDTN: Message displayed
    RDTN->>RDTN: Create DeliveryAck
    RDTN->>T: Send ACK bundle
    T->>SDTN: ACK received
    SDTN->>SDTN: Mark original DEL
    SDTN->>SDTN: Update routing (ACK telemetry)
    SDTN->>S: Update UI (delivered)
```

## Multi-Hop Delivery

When sender and recipient aren't directly connected:

```mermaid
flowchart LR
    A[Sender] -->|copy 1| B[Relay Node 1]
    A -->|copy 2| C[Relay Node 2]
    B -->|forward| D[Recipient]
    C -->|forward| D

    D -->|ACK| B
    B -->|ACK| A
```

**Copy Budget Management:**
- Initial budget: 3 copies (configurable)
- Each forward decrements budget
- When budget = 1, node waits for direct delivery

## Timing

| Phase | Typical Time | Maximum |
|-------|--------------|---------|
| Create & encrypt | < 50ms | 100ms |
| Nearby transfer | 100-500ms | 2s |
| Nostr publish | 200ms-2s | 5s |
| ACK return | Same as forward | - |
| Total (direct) | < 1s | 5s |
| Total (multi-hop) | Minutes to days | TTL (7 days default) |

## Error Handling

| Error | Recovery |
|-------|----------|
| Transport disconnected | Retry with backoff |
| Signature invalid | Drop bundle |
| Duplicate received | Ignore |
| TTL expired | Remove bundle |
| Decryption failed | Store but mark error |

---

**See Also:** [Bundle Lifecycle](../bundles/lifecycle.md) | [Routing Overview](../routing/overview.md)
