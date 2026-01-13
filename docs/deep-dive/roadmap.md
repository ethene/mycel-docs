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
| WiFi Direct transport | Direct WiFi P2P outside Nearby Connections |
| iOS version | Native iOS client |
| Desktop client | macOS/Windows/Linux client |

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
