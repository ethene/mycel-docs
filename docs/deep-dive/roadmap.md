# Roadmap

**Last Updated:** January 2026

This roadmap reflects the current state of development and planned improvements.

## Current Release: v2.1.x

| Feature | Status |
|---------|--------|
| Nearby Connections transport | Stable |
| Nostr relay transport | Stable |
| Direct messages (E2E encrypted) | Complete |
| Private groups (CRDT sync) | Complete |
| PRoPHET + Spray-Wait routing | Complete |
| H3 geo-aware clustering | Complete |
| ACK-based path learning | Complete |

## Current Focus

These features are actively being worked on.

| Feature | Status |
|---------|--------|
| 3+ device stability | In Progress |
| Channels (read-only broadcast) | In Progress |
| Clean Telegram-like UI | In Progress |

## Post-MVP Features

These features are planned but have no committed timeline.

| Feature | Description |
|---------|-------------|
| Pure BLE transport | Direct BLE L2CAP outside Nearby Connections |
| iOS version | Native iOS client |
| Desktop client | macOS/Windows/Linux client |

### WiFi Aware / WiFi Direct

!!! info "Research Complete (January 2026) - No Action Required"
    Nearby Connections already uses WiFi Aware automatically when available.

**Summary:** Standalone WiFi Aware implementation is **not recommended**. Google's Nearby Connections API already selects WiFi Aware when both devices support it.

| Aspect | Finding |
|--------|---------|
| **Device Support** | ~70-80% of modern Android devices |
| **Performance** | 100-250+ Mbps (vs BLE ~1-4 Mbps) |
| **Range** | 50-100m indoor (vs BLE 10-30m) |
| **Power** | Higher than BLE |
| **Nearby Integration** | Already automatic |

**Recommendation:** Rely on Nearby Connections. Benefits:

- Automatic WiFi Aware use when available
- Seamless BLE fallback for unsupported devices
- No additional code to maintain
- Future-proof (Google updates Nearby as tech improves)

**Future consideration:** Direct WiFi Aware may be revisited for iOS interoperability (iOS 17+ has WiFi Aware framework mandated by EU).

## Proposed Routing Improvements

| Feature | Priority | Status |
|---------|----------|--------|
| Nostr Relay Proximity Selection | High | Proposed |
| Nostr Priority Queue | High | Proposed |
| Relay Health Tracking | Medium | Proposed |
| LoRa Transport | Medium | **Researched - Feasible** |
| Transport Cost Model | Low | Proposed |

### LoRa Transport

!!! success "Research Complete (January 2026)"
    LoRa integration via Meshtastic is technically feasible and recommended.

**Summary:** Long-range mesh (1-15 km) using external LoRa hardware connected via BLE/USB.

| Aspect | Finding |
|--------|---------|
| **Recommended Path** | Meshtastic integration via device API |
| **Hardware Cost** | $20-40 per node (Heltec, TTGO, RAK) |
| **Payload Support** | ~240 bytes per packet (meets DTN requirements) |
| **Mesh Routing** | Built-in 3-hop flooding (no additional work) |
| **Effort Estimate** | 4-6 weeks integration |
| **License** | GPL v3 firmware, but external component model avoids conflicts |

**Key Decision:** Treat Meshtastic device as external modem (BLE/serial interface) to maintain Apache 2.0 licensing.

**Regulatory:** ISM bands available globally (EU 868 MHz, US 915 MHz). Duty cycle limits apply (1% EU, 400ms dwell US).

See [LoRa Transport Details](transports/lora.md) for full research findings.

## Ecosystem & Crypto Integration

!!! success "Research Complete (January 2026)"
    Comprehensive analysis of crypto and identity integrations for DTN environments.

### Recommended Integrations

| Integration | Priority | DTN Compatible | Notes |
|-------------|----------|----------------|-------|
| **Cashu E-cash** | High | ✅ Yes | Offline payments via bearer tokens |
| **NIP-77 Negentropy** | High | ✅ Yes | Efficient mesh sync |
| **NIP-BE (BLE transport)** | High | ✅ Yes | Interoperable BLE mesh |
| **DIDs & VCs** | Medium | ✅ Yes | Offline identity/credentials |
| **NIP-05 Identity** | Medium | ⚠️ Partial | Cache when online |
| **Lightning Network** | Low | ⚠️ Partial | Needs watchtowers |

### What Works Offline

| Technology | Offline? | Notes |
|------------|----------|-------|
| Cashu e-cash | ✅ Yes | Bearer tokens, mint verifies on redemption |
| Peer DIDs (did:peer) | ✅ Yes | No blockchain needed |
| Verifiable Credentials | ✅ Yes | Signature verification is local |
| Lightning (local mesh) | ⚠️ Partial | Works if channel graph connected |
| On-chain Bitcoin | ⚠️ Partial | Sign offline, broadcast later |

### What Doesn't Work Offline

| Technology | Why Not |
|------------|---------|
| MobileCoin / Signal-style | Requires online consensus nodes |
| Stablecoins (USDT/USDC) | Blockchain access required |
| Real-time zaps | Lightning routing needs connectivity |
| DNS-based identity | Lookup requires internet |

### Key Decision: Cashu for Offline Payments

Cashu e-cash is the recommended payment integration:

- Users deposit BTC to mint when online, receive tokens
- Tokens transfer P2P via mesh (Nostr events, QR, Bluetooth)
- Recipient redeems when any connectivity available
- Double-spend prevented by mint (first redemption wins)
- Proven: BitChat demonstrated BTC over Bluetooth

See [Ecosystem Integration Details](ecosystem/crypto.md) for full research findings.

## Security

### Implemented

| Feature | Description |
|---------|-------------|
| E2E DM encryption | ECIES: X25519 + HKDF + AES-256-GCM |
| Group key distribution | ECIES-encrypted invites |
| Message signing | Ed25519 signatures |
| Relay blindness | NIP-17 gift-wrap encryption |

### NOT on Roadmap

These features are explicitly **not planned** because they are incompatible with DTN architecture.

| Feature | Reason |
|---------|--------|
| Double Ratchet / Signal Protocol | Requires real-time sessions |
| One-time prekeys | Can't enforce single-use in DTN |
| Per-message key advancement | Can't guarantee message ordering |
| Sender Keys with ratchet | Requires real-time group coordination |

## Release History

| Version | Date | Highlights |
|---------|------|------------|
| v2.1.0 | 2026-01-12 | Iran soft launch, Farsi L10N (486 strings), bilingual invite |
| v2.0.724 | 2026-01-02 | Nostr enabled by default |
| v2.0.723 | 2026-01-02 | FGS permission fix |
| v2.0.722 | 2026-01-02 | Gson TypeToken fix, dynamic versioning |
| v2.0.721 | 2026-01-02 | H3 JNI ProGuard fix |

---

*No specific timelines are committed. Development is prioritized by user impact and stability.*
