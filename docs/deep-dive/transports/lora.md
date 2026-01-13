# LoRa Transport (Planned)

This page documents the planned LoRa integration for Mycel, based on research completed January 2026.

!!! info "Status: Researched - Not Yet Implemented"
    LoRa integration has been validated as technically feasible. Implementation is planned for a future release.

---

## Overview

LoRa (Long Range) radio provides kilometer-scale mesh communication for areas without cellular coverage. Mycel plans to integrate LoRa via the **Meshtastic** ecosystem.

| Property | Value |
|----------|-------|
| **Range** | 1-15 km (line-of-sight) |
| **Data Rate** | 36-700 bytes/sec (depending on settings) |
| **Frequency** | Sub-GHz ISM bands (868/915 MHz) |
| **Hardware** | External LoRa module ($20-40) |
| **Implementation** | Via Meshtastic device API |

---

## Why LoRa?

Current Mycel transports have range limitations:

| Transport | Range | Limitation |
|-----------|-------|------------|
| Nearby Connections (BLE) | 30-50m | Short range |
| Nearby Connections (WiFi) | Up to 200m | Requires WiFi hardware |
| Nostr Relays | Global | Requires internet |

LoRa fills the gap: **kilometer-scale offline communication**.

---

## Meshtastic Integration

### Why Meshtastic?

[Meshtastic](https://meshtastic.org) is an open-source LoRa mesh project with:

- Proven multi-hop mesh routing (3-hop flooding)
- Support for arbitrary binary payloads
- Extensive hardware compatibility
- Active community and ongoing development

### Integration Approach

Mycel will treat Meshtastic as an **external transport** (like a modem):

```
┌─────────────┐     BLE/USB      ┌─────────────────┐     LoRa     ┌─────────────┐
│  Mycel App  │ ◄──────────────► │ Meshtastic Node │ ◄──────────► │ Other Nodes │
└─────────────┘                  └─────────────────┘              └─────────────┘
```

**Benefits:**

- No firmware development required
- Meshtastic handles mesh routing
- License isolation (GPL firmware, Apache app)
- Leverage existing community testing

### License Compatibility

| Component | License | Our Approach |
|-----------|---------|--------------|
| Meshtastic firmware | GPL v3 | Runs on external hardware |
| Meshtastic Python lib | GPL v3 | Reference only, not included |
| Mycel LoRa bridge | Apache 2.0 | Clean-room implementation |

By treating Meshtastic as an external device (like a printer or modem), Mycel maintains Apache 2.0 licensing.

---

## Hardware Options

Users will need a LoRa module to use this transport. Recommended devices:

| Device | Price | Interface | Range | Notes |
|--------|-------|-----------|-------|-------|
| **Heltec WiFi LoRa 32 V3** | ~$20 | USB-C, BLE | 5-10 km | Recommended starter |
| **LilyGO TTGO LoRa32** | ~$25 | USB, BLE | 3-10 km | OLED display included |
| **RAK WisBlock RAK4631** | ~$35 | USB, BLE | 5-15 km | Modular, expandable |
| **LilyGO T-Beam** | ~$40 | USB, BLE | 3-10 km | Includes GPS |

All devices use the Semtech SX1262 or SX1276 LoRa chipset and are supported by Meshtastic firmware.

### Connection Methods

| Method | Pros | Cons |
|--------|------|------|
| **BLE** | Low phone power, device runs on battery | Requires pairing |
| **USB OTG** | Simple setup | Drains phone battery |

---

## Technical Specifications

### Payload Capacity

| Setting | Data Rate | Airtime (250B) | Range |
|---------|-----------|----------------|-------|
| SF7 @ 125kHz | ~700 B/s | 0.4 sec | 2-3 km |
| SF9 @ 125kHz | ~150 B/s | 1.7 sec | 5-8 km |
| SF12 @ 125kHz | ~36 B/s | 7 sec | 10-15 km |

Mycel DTN bundles (~250 bytes) fit within a single LoRa packet.

### Regulatory Compliance

LoRa operates in **unlicensed ISM bands** worldwide:

| Region | Frequency | Power Limit | Duty Cycle |
|--------|-----------|-------------|------------|
| Europe (EU868) | 863-870 MHz | 14 dBm (25 mW) | 1% |
| North America (US915) | 902-928 MHz | 30 dBm (1 W) | 400ms dwell |
| Australia (AU915) | 915-928 MHz | 30 dBm | No limit |
| Asia (AS923) | 920-923 MHz | 16 mW | LBT required |
| India | 865-867 MHz | 27 dBm | No formal limit |

!!! warning "Duty Cycle Limits"
    In Europe, the 1% duty cycle means ~36 seconds of airtime per hour per channel. This is sufficient for delay-tolerant messaging but prevents continuous transmission.

---

## Implementation Plan

### Phase 1: BLE Bridge

1. Implement Meshtastic BLE protocol in Mycel
2. Device discovery and pairing UI
3. Send/receive DTN bundles via private port
4. Basic testing with 2-3 nodes

### Phase 2: Routing Integration

1. Register LoRa as transport with `TransportKind.MID_RANGE`
2. Configure routing priority (below Nearby, above Nostr for local)
3. Handle MTU constraints (chunk larger messages)
4. Implement retry logic for duty cycle limits

### Effort Estimate

| Phase | Duration | Dependencies |
|-------|----------|--------------|
| Phase 1 | 4-6 weeks | Meshtastic protocol docs |
| Phase 2 | 2-3 weeks | Phase 1 complete |

---

## Alternative: Custom LoRa Stack

Building a custom LoRa mesh (without Meshtastic) was evaluated but **not recommended**:

| Aspect | Meshtastic | Custom |
|--------|------------|--------|
| Development time | 4-6 weeks | 4-6 months |
| Mesh routing | Included | Must implement |
| Hardware support | 50+ devices | Must add each |
| Community testing | Yes | No |
| Maintenance | Shared | Full ownership |

Custom development may be revisited if Meshtastic proves unsuitable.

---

## Related Projects

- **[Meshtastic](https://meshtastic.org)** - LoRa mesh firmware
- **[MeshCore](https://github.com/meshcore)** - Alternative LoRa mesh (emerging)
- **[Reticulum](https://reticulum.network)** - General-purpose mesh networking
- **[LXMF](https://github.com/markqvist/LXMF)** - Lightweight mesh messaging on Reticulum

---

**See also:** [Nearby Connections](nearby.md) | [Nostr Transport](nostr.md) | [Roadmap](../roadmap.md)
