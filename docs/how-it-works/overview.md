# How Mycel Works

A technical overview of Mycel's architecture.

## Architecture

Mycel consists of three main layers:

```
┌─────────────────────────────────────┐
│           Application Layer          │
│     (UI, Contacts, Conversations)    │
├─────────────────────────────────────┤
│             DTN Layer                │
│   (Bundles, Routing, Store/Forward)  │
├─────────────────────────────────────┤
│          Transport Layer             │
│      (Nearby, Nostr, Future LoRa)    │
└─────────────────────────────────────┘
```

### Application Layer

- User interface (Jetpack Compose)
- Contact management
- Conversation view
- Settings and identity

### DTN Layer

The core of Mycel - implements the Bundle Protocol:

- **Bundles** - Message containers with headers and payload
- **Routing** - Decides which path(s) to send messages
- **Store-and-Forward** - Persists messages until delivered
- **Acknowledgments** - Delivery confirmation

### Transport Layer

Multiple transports work together:

- **Nearby** - Google Nearby Connections (BLE + Wi-Fi)
- **Nostr** - Internet relay (optional fallback)
- **Future** - LoRa, other radios

## The Bundle Protocol

Messages are wrapped in **bundles** - self-contained packets that can be stored and forwarded independently.

```
Bundle {
    Header {
        id: unique identifier
        source: sender's public key
        destination: recipient's public key
        created: timestamp
        ttl: time-to-live
        routePath: nodes that have handled this bundle
    }
    Payload {
        encrypted message content
    }
    Signature {
        Ed25519 signature from sender
    }
}
```

## Routing

Mycel uses a hybrid routing approach:

### Spray-and-Wait

- Messages start with a **copy budget** (e.g., 3 copies)
- Copies are distributed to encountered devices
- Once budget is exhausted, messages wait for direct delivery

### PRoPHET (Probabilistic Routing)

- Tracks delivery probability to each destination
- Higher probability = more likely to successfully relay
- Updated based on encounters and successful deliveries

### Interest-Based Routing

- Devices exchange "bloom filters" of known contacts
- Messages preferentially sent to devices that know the destination

## Security

### End-to-End Encryption

- **X25519** Diffie-Hellman key agreement
- **HKDF-SHA256** key derivation
- **AES-256-GCM** authenticated encryption
- Only sender and recipient can decrypt

### Signatures

- **Ed25519** signatures on all bundles
- Verifies sender identity
- Prevents tampering

### No Central Authority

- No servers to trust
- No single point of failure
- No metadata collection

## Identity

Users are identified by their **Ed25519 public key**:

1. Private key generated on device (never leaves)
2. Public key = your identity
3. **Device ID** = truncated hash of public key (for human readability)

## Data Persistence

Mycel uses **Room** (SQLite) for local storage:

- Bundles waiting for delivery
- Delivered messages
- Contact information
- Routing tables

No data is stored externally.
