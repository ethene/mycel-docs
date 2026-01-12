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
| LoRa Transport | Medium | Proposed |
| Transport Cost Model | Low | Proposed |

### LoRa Transport

LoRa would enable long-range, low-power mesh communication:

- Sub-GHz frequencies for kilometer-range links
- Designed for areas with no cellular coverage
- Architecture designed, implementation not started

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
