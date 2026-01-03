# Encryption

All message content in Mycel is end-to-end encrypted. Relay nodes cannot read message contents.

## Overview

| Destination Type | Encryption Scheme |
|------------------|-------------------|
| Direct Message (DM) | ECIES: X25519 + HKDF + AES-256-GCM |
| Group Message | AES-256-GCM with shared group key |
| Channel Broadcast | AES-256-GCM with channel key |
| Group Invite | ECIES: X25519 + XChaCha20-Poly1305 |

## DM Encryption (ECIES)

Direct messages use Elliptic Curve Integrated Encryption Scheme.

### Encryption Flow

```mermaid
flowchart TB
    subgraph Sender
        S1[Recipient Ed25519 pubkey]
        S2[Convert to X25519]
        S3[Generate ephemeral X25519 keypair]
        S4[X25519 ECDH → shared secret]
        S5[HKDF-SHA256 → AES key]
        S6[AES-256-GCM encrypt]
    end

    subgraph Ciphertext
        C1[ciphertext]
        C2[ephemeral_pub 32 bytes]
        C3[nonce 12 bytes]
    end

    S1 --> S2 --> S4
    S3 --> S4
    S4 --> S5 --> S6
    S6 --> C1
    S3 --> C2
```

### Algorithm Steps

1. **Key Conversion:** Recipient's Ed25519 public key → X25519 public key
2. **Ephemeral Key:** Generate random X25519 keypair
3. **ECDH:** Compute shared secret using ephemeral private + recipient public
4. **KDF:** HKDF-SHA256 with info="DmEncryption" → 32-byte AES key
5. **Encrypt:** AES-256-GCM with random 12-byte nonce
6. **Package:** ciphertext + ephemeral_pub + nonce

### Decryption Flow

```mermaid
flowchart TB
    subgraph Recipient
        R1[Extract ephemeral_pub from ciphertext]
        R2[Own Ed25519 privkey → X25519 privkey]
        R3[X25519 ECDH → shared secret]
        R4[HKDF-SHA256 → AES key]
        R5[AES-256-GCM decrypt]
        R6[Plaintext message]
    end

    R1 --> R3
    R2 --> R3
    R3 --> R4 --> R5 --> R6
```

## Group Message Encryption

Group messages use symmetric encryption with a shared group key.

### Group Key Distribution

1. Group creator generates 32-byte random group key
2. Key is encrypted per-member using ECIES (GroupInviteMsg)
3. All members have the same symmetric key

### Encryption

Messages are encrypted with **AES-256-GCM** using the shared group key and a random 12-byte nonce.

## Group Invite Encryption

Group invites use ECIES with XChaCha20-Poly1305 for the symmetric cipher.

### GroupInviteMsg Format

| Field | Size | Description |
|-------|------|-------------|
| encryptedPayload | Variable | XChaCha20-Poly1305 encrypted invite |
| ephemeralPubKey | 32 bytes | X25519 ephemeral public key |
| nonce | 24 bytes | XChaCha20 nonce |

### Encrypted Payload Contents

The encrypted payload contains:
- Group ID
- Group display name
- Group symmetric key (32 bytes)
- Admin public key
- Invite timestamp

## Nostr Transport Encryption (Gift-Wrap)

When messages traverse Nostr relays, additional encryption prevents relay metadata correlation.

**Protocol:** NIP-17 (Gift-Wrap) + NIP-44 (Versioned Encryption)

### Gift-Wrap Layers

```mermaid
flowchart TB
    subgraph Original
        M[Message Content]
    end

    subgraph Sealed["Sealed (NIP-44)"]
        S1[Encrypted to recipient]
        S2[Rumor event]
    end

    subgraph Gift["Gift-Wrapped"]
        G1[Encrypted sealed event]
        G2[Random pubkey as author]
        G3[Randomized timestamp]
    end

    M --> S1
    S1 --> S2
    S2 --> G1
    G1 --> G2
    G2 --> G3
```

### Properties

| Property | Benefit |
|----------|---------|
| Random author pubkey | Hides sender identity from relays |
| Randomized timestamp | Prevents timing correlation |
| Double encryption | Recipient identity hidden from relays |

## Security Properties

### Forward Secrecy
- Ephemeral keys ensure past messages cannot be decrypted if long-term key is compromised
- Each DM uses a unique ephemeral keypair

### Relay Blindness
- Nostr relays only see encrypted gift-wrapped events
- Cannot correlate sender/recipient
- Cannot read message contents

### Authentication
- All bundles are signed with Ed25519
- Signature covers header + payload hash
- Prevents tampering and forgery

## What's NOT Implemented

Some advanced security features are intentionally not implemented due to DTN constraints:

| Feature | Reason Not Used |
|---------|-----------------|
| Double Ratchet | Requires synchronized sessions (impossible in DTN) |
| One-time prekeys | Cannot enforce single-use without server |
| Per-message key advancement | Messages arrive out of order |

---

**Next:** [Bundle Structure](structure.md) | [Message Types](message-types.md)
