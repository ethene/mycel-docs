# Roadmap

**Last Updated:** January 2026

This roadmap reflects the current state of development and planned improvements. All information is verified against source documentation.

## Current Release: v2.0.x

| Feature | Status | Verified Source |
|---------|--------|-----------------|
| Nearby Connections transport | Stable | `PROJECT_VISION.md` line 34 |
| Nostr relay transport | Stable | `PROJECT_VISION.md` line 35 |
| Direct messages (E2E encrypted) | Complete | `docs/security/SECURITY_ROADMAP.md` |
| Private groups (CRDT sync) | Complete | `PROJECT_VISION.md` line 148 |
| PRoPHET + Spray-Wait routing | Complete | `docs/routing/ROUTING_OVERVIEW.md` |
| H3 geo-aware clustering | Complete | `PROJECT_VISION.md` lines 57-59 |
| ACK-based path learning | Complete | `PROJECT_VISION.md` lines 53-55 |
| DeliveryAck symmetry | Complete | Session #83 |

## Current Focus

These features are actively being worked on.

| Feature | Status | Source |
|---------|--------|--------|
| 3+ device stability | In Progress | `PROJECT_VISION.md` line 138 |
| Channels (read-only broadcast) | In Progress | `PROJECT_VISION.md` line 149 |
| Clean Telegram-like UI | In Progress | `PROJECT_VISION.md` line 153 |
| OEM battery whitelist UI | In Progress | Session #87 |

## Post-MVP Features

These features are documented but have no committed timeline.

**Source:** `PROJECT_VISION.md` lines 156-159

| Feature | Description |
|---------|-------------|
| Pure BLE transport | Direct BLE L2CAP outside Nearby Connections |
| WiFi Direct transport | Direct WiFi P2P outside Nearby Connections |
| iOS version | Native iOS client |
| Desktop client | macOS/Windows/Linux client |

## Proposed Routing Improvements

These are proposed improvements documented in the codebase but not yet implemented.

**Source:** `docs/routing/FUTURE_ROADMAP.md`

| Feature | Priority | Status |
|---------|----------|--------|
| Nostr Relay Proximity Selection | High | Proposed |
| Nostr Priority Queue | High | Proposed |
| Relay Health Tracking | Medium | Proposed |
| LoRa Transport | Medium | Proposed (architecture sketch exists) |
| Transport Cost Model | Low | Proposed |

### LoRa Transport Details

LoRa transport is documented as a **proposed** improvement with an architecture sketch.

**Source:** `docs/routing/FUTURE_ROADMAP.md` lines 62-123

- Would enable long-range, low-power communication
- Architecture designed but not implemented
- No implementation timeline committed

## Security Roadmap

Current security implementation and what's explicitly NOT planned.

**Source:** `docs/security/SECURITY_ROADMAP.md`

### Implemented

| Feature | Description |
|---------|-------------|
| E2E DM encryption | ECIES: X25519 + HKDF + AES-256-GCM |
| Group key distribution | ECIES-encrypted invites |
| Message signing | Ed25519 signatures |
| Relay blindness | NIP-17 gift-wrap encryption |

### NOT on Roadmap

These features are explicitly **not planned** because they are incompatible with DTN.

**Source:** `docs/security/SECURITY_ROADMAP.md` lines 131-140

| Feature | Reason |
|---------|--------|
| Double Ratchet / Signal Protocol | Breaks in DTN (requires real-time sessions) |
| One-time prekeys | Can't enforce single-use in DTN |
| Per-message key advancement | Can't guarantee message ordering |
| Sender Keys with member-change ratchet | Requires real-time group coordination |

## Research Topics

These topics have been researched and synthesized but await implementation decisions.

**Source:** `docs/research/` directory

| Topic | Synthesis | Status |
|-------|-----------|--------|
| T1: Failure Detection | Route penalty, aging | Some implemented |
| T2: Self-Healing | FSM, exploration | Designed |
| T3: Loop Prevention | Hop tracking | Designed |
| T4: Priority Queuing | LLQ scheduling | Designed |
| T5: Adaptive TTL | Time+hop hybrid | Some implemented |
| T6: H3 Clustering | Gateway detection | Partially implemented |
| T7: Global Directory | Decentralized search | Designed |

See `docs/design/SELF_HEALING_DTN_PROTOCOL.md` for the unified protocol specification.

## Release History

| Version | Date | Highlights |
|---------|------|------------|
| v2.0.724 | 2026-01-02 | Nostr enabled by default |
| v2.0.723 | 2026-01-02 | FGS permission fix |
| v2.0.722 | 2026-01-02 | Gson TypeToken fix, dynamic versioning |
| v2.0.721 | 2026-01-02 | H3 JNI ProGuard fix |

---

*No specific timelines are committed for future features. Development is prioritized by user impact and stability requirements.*
