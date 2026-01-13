# Nearby Connections Stack

This page documents how Mycel uses Google Nearby Connections for local mesh networking.

---

## Overview

Mycel's primary local transport uses **Google Nearby Connections API**, which provides:

- Bluetooth Low Energy (BLE) for discovery
- WiFi Direct for high-bandwidth data transfer
- Automatic medium selection based on conditions

This is the foundation of Mycel's offline mesh networking.

---

## How It Works

### Connection Strategy

Mycel uses the **P2P_CLUSTER** strategy:

- All devices can discover and connect to each other
- No master/slave hierarchy
- Supports multiple simultaneous connections
- Optimized for mesh topologies

### Discovery Flow

```
1. App starts → Begins advertising + discovering
2. Nearby device found → Connection requested
3. Both devices accept → Encrypted channel established
4. Ready for message exchange
```

### Data Transfer

Once connected, devices can:

- Send DTN bundles (encrypted messages)
- Exchange routing information
- Relay messages for other users

---

## Physical Specifications

| Property | Typical Value | Notes |
|----------|---------------|-------|
| **BLE Discovery Range** | 30-50 meters | Line-of-sight, varies by device |
| **WiFi Direct Range** | Up to 200 meters | When available |
| **Connection Slots** | 4 per device | Configurable limit |
| **Handshake Time** | 2-5 seconds | Device dependent |

!!! note "Range varies"
    Real-world range depends on device hardware, obstacles, interference, and battery state. Indoor environments typically have shorter range.

---

## Requirements

### Device Requirements

| Requirement | Details |
|-------------|---------|
| **Android Version** | 8.0+ (API 26+), full features on 10+ |
| **Google Play Services** | Required (provides Nearby API) |
| **Bluetooth** | Must be enabled |
| **Location** | Permission required for BLE scanning |
| **WiFi** | Should be enabled for best performance |

### Huawei Limitation

Huawei devices manufactured after 2019 typically lack Google Play Services and **cannot run Mycel**. This is a fundamental limitation.

---

## Transport Stack

Nearby Connections internally manages multiple mediums:

| Medium | Used For | Controlled By |
|--------|----------|---------------|
| **BLE** | Discovery, small payloads | GMS |
| **WiFi Direct** | Large data transfers | GMS |
| **WiFi Aware** | Fast discovery (if available) | GMS |
| **Classic Bluetooth** | Fallback | GMS |

Mycel doesn't control which medium is used - Google Play Services makes these decisions automatically based on:

- Device capabilities
- Signal strength
- Power state
- Data size

---

## Self-Healing System

Mycel includes automatic recovery mechanisms:

### Watchdog

A background watchdog monitors connection health:

| Check | Interval | Action |
|-------|----------|--------|
| Discovery stall | 30 seconds | Restart discovery |
| Connection timeout | 60 seconds | Reconnection attempt |
| Session health | Continuous | Clean up dead sessions |

### Recovery Escalation

When connections fail, Mycel escalates through recovery levels:

1. **Queue retry** - Retry pending transfers
2. **Connection bounce** - Disconnect and reconnect
3. **Bluetooth toggle** - Reset Bluetooth state
4. **Service restart** - Restart mesh service
5. **Device restart** - Prompt user (last resort)

### Peer Tracking

Mycel maintains a directory of discovered peers:

- Tracks last-seen timestamps
- Maintains 1-hour history
- Prioritizes recently-seen peers for reconnection

---

## Known Limitations

### GMS Dependency

Mycel **requires Google Play Services**. Without it:

- Nearby Connections API is unavailable
- The app cannot function
- This affects custom ROMs and some Chinese devices

### Background Discovery

Android limits Bluetooth scanning in the background:

- Error code 8034 may appear
- Mitigated by foreground service
- Battery optimization can interfere

### Multi-Device Stability

Current known issue:

- 2-device connections: Stable
- 3+ device connections: Can be unstable
- Being actively worked on

---

## Alternative Transports

### Standalone BLE (Disabled)

Mycel previously had a standalone BLE implementation using:

- L2CAP (API 29+) for connection-oriented channels
- GATT for legacy device support

This was **disabled** due to Bluetooth stack corruption issues. Nearby Connections is currently the sole local transport.

### WiFi Aware

WiFi Aware (NAN - Neighbor Awareness Networking) is detected if available but not explicitly used beyond what Nearby Connections chooses internally.

---

## Debugging

### Diagnostics

Check Nearby status in the app:

1. Go to **Mesh Lab** tab
2. Select **Nearby**
3. View:
   - Connected neighbors
   - Active sessions
   - Discovery status
   - Recent errors

### Developer Tools

Using the bridge CLI:

```bash
# Full diagnostics
meshctl --serial DEVICE nearby diag

# Session states
meshctl --serial DEVICE nearby sessions

# Watchdog status
meshctl --serial DEVICE nearby watchdog

# Discovery info
meshctl --serial DEVICE nearby discovery
```

---

## Future Directions

### LoRa Integration

Long-range radio transport is being researched:

- Potential integration with Meshtastic ecosystem
- Would provide kilometer-scale range
- Low bandwidth (~250 bytes/message)

### Pure BLE Transport

Re-enabling standalone BLE (outside Nearby) is possible once Bluetooth stack issues are resolved:

- Would reduce GMS dependency
- Better control over connection parameters
- Useful for specific use cases

---

**See also:** [Transports Overview](transports.md) | [Nostr Transport](../deep-dive/transports/nostr.md)
