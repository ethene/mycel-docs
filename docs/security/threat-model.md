# Threat Model

This document describes what Mycel protects against, what it doesn't, and the security assumptions we make.

## Security Goals

Mycel aims to provide:

1. **Confidentiality** - Only sender and recipient can read messages
2. **Authenticity** - Messages provably come from claimed sender
3. **Censorship resistance** - No single point of control
4. **Privacy** - Minimal metadata exposure
5. **Offline operation** - Works without internet infrastructure

## Adversary Capabilities

We consider adversaries with varying capabilities:

### Passive Network Observer

**Can:** Monitor network traffic, see encrypted payloads
**Cannot:** Read message contents, forge messages

**Mycel's defense:** End-to-end encryption, signed bundles

### Relay Node Operator

**Can:** See bundle metadata (source, destination, size, timing)
**Cannot:** Read message contents, prevent forwarding

**Mycel's defense:** Encryption makes content unreadable; mesh topology means messages can route around hostile nodes

### Nostr Relay Operator

**Can:** See encrypted events, connection timing, IP addresses
**Cannot:** Read message contents, identify users (unless IP reveals identity)

**Mycel's defense:** NIP-17 gift-wrap encryption, optional relay usage

### Device Thief

**Can:** Access all local data if device is unlocked
**Cannot:** Access data if device has screen lock (unless bypassed)

**Mycel's defense:** None beyond Android's device encryption. We don't implement app-level encryption.

### Sophisticated Actor (Nation-State)

**Can:** Potentially monitor many relay nodes, perform traffic analysis, compromise devices
**Cannot:** Break cryptographic primitives

**Mycel's defense:** Partial. Spray-and-Wait creates multiple paths. Strong crypto protects content. But traffic analysis may reveal communication patterns.

## What's Protected

| Property | Protection Level | Notes |
|----------|------------------|-------|
| Message content | Strong | AES-256-GCM encryption |
| Sender authenticity | Strong | Ed25519 signatures |
| Message integrity | Strong | Signature verification |
| Sender identity | Partial | Public key visible to relays |
| Recipient identity | Partial | Public key visible to relays |
| Communication timing | Weak | Approximate timing visible to relays |
| Communication existence | Weak | Observers can see "message sent" |

## What's NOT Protected

### Endpoint Security

If your device is compromised (malware, physical access), all bets are off. Mycel cannot protect against:

- Keyloggers capturing messages as you type
- Screen capture malware
- Device theft with unlocked phone
- Malicious Mycel builds

### Traffic Analysis

A sophisticated adversary monitoring multiple relay nodes could potentially:

- Correlate message timing to identify communication partners
- Estimate message frequency between users
- Build social graphs from observation

### Metadata at Nostr Relays

When using Nostr transport:

- Your IP address is visible to relay operators
- Connection timing reveals activity patterns
- Relay operators could log and correlate

### Future Key Compromise

Mycel does **not** provide forward secrecy:

- If your private key is compromised, all past messages can be decrypted
- This is a deliberate trade-off for DTN compatibility
- Signal-style ratcheting requires synchronized sessions

## Cryptographic Primitives

| Use Case | Algorithm | Key Size |
|----------|-----------|----------|
| Key agreement | X25519 (ECDH) | 256-bit |
| Symmetric encryption | AES-256-GCM | 256-bit |
| Key derivation | HKDF-SHA256 | - |
| Signing | Ed25519 | 256-bit |
| Hashing | SHA-256 | 256-bit |

These primitives are well-studied and considered secure against classical computers.

## Security Assumptions

Mycel's security relies on:

1. **Cryptographic hardness** - X25519, AES-256, Ed25519 remain secure
2. **Random number generation** - Android's SecureRandom works correctly
3. **No device compromise** - Your phone isn't rooted/malware-infected
4. **Key secrecy** - Your private key hasn't leaked

## Known Limitations

### No Perfect Forward Secrecy

Traditional forward secrecy requires:
- Real-time key negotiation
- Synchronized sessions
- Both parties online

This is incompatible with DTN where messages may arrive days later.

**Implication:** Compromise of your private key compromises all past messages.

### No Deniability

Messages are cryptographically signed. You cannot plausibly deny having sent a message - the signature proves it.

### Limited Anonymity

Your public key is visible to relay nodes. If your key is associated with your real identity (e.g., you shared it publicly), relays learn you're communicating.

## Threat Comparison

| Threat | Protection |
|--------|------------|
| Mass surveillance | Strong - No central point to tap |
| Targeted surveillance | Partial - Traffic analysis possible |
| Eavesdropping | Strong - E2E encryption |
| Message tampering | Strong - Signatures |
| Impersonation | Strong - Signatures |
| Censorship | Strong - No single point of control |
| Device theft (locked) | Depends on Android encryption |
| Device theft (unlocked) | None |
| Malware on device | None |
| Rubber hose cryptanalysis | None |

## Recommendations

### For Casual Users

1. Use device screen lock
2. Keep Mycel updated
3. Verify contacts' identities in person

### For High-Risk Users

1. All of the above, plus:
2. Consider disabling Nostr (mesh-only mode)
3. Use a dedicated device
4. Verify APK checksums
5. Assume sophisticated adversaries can perform traffic analysis
6. Consider message content carefully - encryption protects transit, not endpoints

---

**See Also:** [Privacy](privacy.md) | [APK Verification](verification.md) | [Encryption Deep Dive](../deep-dive/bundles/encryption.md)
