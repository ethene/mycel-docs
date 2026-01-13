# Vision

**Mycel is a messenger for people without reliable internet access.**

---

## The Problem

**3.7 billion people** lack reliable internet access. They can't use modern messaging apps. Existing solutions require:

- Constant internet connectivity (WhatsApp, Telegram, Signal)
- Expensive satellite hardware (Starlink)
- Complex infrastructure (LoRa networks)

**Mycel works with what people already have**: Android phones with Bluetooth.

---

## How Mycel Works

Mycel uses **true DTN (Delay-Tolerant Networking)**: messages hop between phones until they reach their destination - across a village, a city, or the globe.

```
Alice's phone → [stores message] → Bob walks by → [message hops] →
→ [hops again] → ... → reaches recipient (hours/days later)
```

Messages persist on devices and forward opportunistically when other Mycel users come within Bluetooth range. **No base stations. No internet required.**

### Delivery Can Take Time

This isn't a bug - it's a feature. Mycel trades instant delivery for the ability to work **anywhere**:

| Scenario | Typical Time |
|----------|--------------|
| Same room | Seconds |
| Same neighborhood | Minutes to hours |
| Same city | Hours |
| Different cities | Hours to days |
| Remote areas | Days or longer |

---

## Unified Transport Model

All transports form **ONE logical network**. Users see no difference between local mesh and global internet routing.

| Transport | Type | Scope |
|-----------|------|-------|
| **Nearby Connections** | BLE/WiFi | Local mesh (100-300m) |
| **Nostr Relays** | WebSocket | Global via internet relays |
| **Future: LoRa** | Radio | Long-range (kilometers) |

Routing automatically picks the best available path. When you have internet, messages go faster. When you don't, they travel through the mesh.

---

## Core Principles

### 1. Offline First

Internet is a bonus, not a requirement. Every feature works without connectivity.

### 2. Uses Existing Hardware

No special equipment needed - just an Android phone with Bluetooth.

### 3. Privacy by Design

- End-to-end encryption for all messages
- Rotating identities prevent tracking
- No central server stores your data

### 4. Simple for Users

Complex routing hidden behind a clean UI. You send a message; it finds its way.

### 5. Resilient

Messages eventually arrive despite network failures, phone reboots, and interrupted connections.

---

## Message Types

| Type | Description | Encryption |
|------|-------------|------------|
| **Direct Messages** | Person-to-person | E2E encrypted |
| **Private Groups** | Invite-only, any member can post | Shared group key |
| **Channels** | Admin-only broadcast | Signed by admin |

---

## Target Users

### Primary

People in areas with unreliable or no internet:

- Rural communities
- Developing regions
- Disaster/emergency scenarios
- Protest situations
- Events with overloaded networks

### Secondary

Privacy-conscious users who want local-first messaging without corporate servers.

---

## Technical Foundation

Mycel is built on proven technologies:

| Component | Technology |
|-----------|------------|
| **Protocol** | DTN (Delay-Tolerant Networking) |
| **Routing** | PRoPHET + Spray-and-Wait hybrid |
| **Encryption** | X25519 key exchange, AES-256-GCM |
| **Signatures** | Ed25519 |
| **Local Transport** | Google Nearby Connections (BLE/WiFi) |
| **Internet Transport** | Nostr relays (NIP-17) |
| **Geographic Routing** | H3 hexagonal grid |

---

## The Future

Mycel is evolving toward a broader ecosystem:

- **LoRa integration** for long-range mesh (potentially via Meshtastic)
- **Ecosystem integrations** with the broader decentralized web
- **iOS and desktop** clients (long-term)

Our goal: **Messages find a way.**

---

## Open Source

Mycel is open source software. The codebase is available for inspection and contribution.

---

*"Mycel: Messages find a way."*
