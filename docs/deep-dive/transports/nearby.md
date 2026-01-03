# Nearby Connections Transport

Nearby Connections is the primary short-range transport, using BLE for discovery and WiFi Direct for high-speed data transfer.

## Overview

| Property | Value |
|----------|-------|
| **Technology** | Google Play Services Nearby Connections API |
| **Discovery** | Bluetooth Low Energy (BLE) |
| **Transfer** | WiFi Direct (high bandwidth) or BLE (fallback) |
| **Range** | ~100m line-of-sight, ~30m typical indoor |
| **Bandwidth** | Up to 400+ Mbps (WiFi Direct) |

## Session State Machine

Each peer connection follows an 8-state finite state machine.

**Source:** `core/nearby/src/main/kotlin/com/meshlablite/core/nearby/session/SessionRegistry.kt:14`

```mermaid
stateDiagram-v2
    [*] --> IDLE

    IDLE --> DISCOVERY : endpoint found
    DISCOVERY --> IDLE : endpoint lost
    DISCOVERY --> CONNECTING : connect scheduled

    CONNECTING --> IDLE : timeout/failure
    CONNECTING --> SESSION_OPEN : connection success

    SESSION_OPEN --> SYNC : ctrl sync begin
    SESSION_OPEN --> XFER : transfer start

    SYNC --> XFER : sync ready

    XFER --> DRAIN : queues empty
    DRAIN --> XFER : new data
    DRAIN --> CLOSING : quiet idle

    CLOSING --> IDLE : cleanup

    note right of XFER : Transfer-ready
    note right of DRAIN : Transfer-ready
```

### State Reference

| State | Connected? | Can Transfer? | Description |
|-------|------------|---------------|-------------|
| `IDLE` | No | No | Default/disconnected. Set on disconnect, timeout, or endpoint unknown. |
| `DISCOVERY` | No | No | Endpoint discovered during BLE scan. |
| `CONNECTING` | No | No | Connection attempt in progress. |
| `SESSION_OPEN` | Yes | No | Nearby connection established, control session created. |
| `SYNC` | Yes | No | Control sync/handshake in progress. |
| `XFER` | Yes | **Yes** | Actively transferring data. |
| `DRAIN` | Yes | **Yes** | Queues drained, idle but connected. |
| `CLOSING` | No | No | Sending BYE, tearing down. |

**Critical:** Only `XFER` and `DRAIN` states can transfer bundles.

### State Transitions

| Transition | Trigger | Source |
|------------|---------|--------|
| IDLE → DISCOVERY | Endpoint found via BLE scan | `DiscoveryCallbackFactory.kt:40-76` |
| DISCOVERY → CONNECTING | Connect attempt scheduled | `ConnectionCoordinator.kt:327-333` |
| CONNECTING → SESSION_OPEN | Connection success callback | `DefaultSessionController.kt:20-33` |
| SESSION_OPEN → SYNC | Control sync begins | `NearbyCore.kt:1652-1655` |
| SYNC → XFER | Sync complete, ready | `NearbyCore.kt:1652-1655` |
| XFER → DRAIN | All queued data sent | `NearbyCore.kt:3225-3229` |
| DRAIN → CLOSING | Quiet idle timeout | `ConnectionCoordinator.kt:480-497` |
| * → IDLE | Disconnect or timeout | `NearbyCore.kt:1245, 1284-1289` |

## Control Session Protocol

After Nearby connection is established, a control session is created for bundle exchange.

### Handshake

1. **Hello**: Exchange device identities (DeviceId, capabilities)
2. **HelloProof**: Verify identity via signature
3. **Sync**: Exchange routing tables (PRoPHET P-values)
4. **Ready**: Session is `XFER`-ready

### Transfer Types

| Type | Size | Method |
|------|------|--------|
| Small payloads | < 32KB | Inline in control messages |
| Large payloads | 32KB+ | Nearby `sendPayload()` stream |

### Bundle Selection

Before transfer, the routing layer selects bundles:

```kotlin
// From RoutingFacade.kt:514-567
fun selectBundlesForPeer(peerId: PeerId): List<Bundle> {
    return bundleRepo.getForwardable()
        .filter { shouldSend(it, peerId) }
        .sortedBy { it.priority }
        .take(MAX_BATCH_SIZE)
}
```

## Connection Lifecycle

### Discovery

```mermaid
sequenceDiagram
    participant Local as Local Device
    participant GMS as Google Play Services
    participant Remote as Remote Device

    Local->>GMS: startAdvertising()
    Local->>GMS: startDiscovery()
    Remote->>GMS: startAdvertising()

    GMS->>Local: onEndpointFound(endpointId)
    Note over Local: State: DISCOVERY
```

### Connection

```mermaid
sequenceDiagram
    participant Local as Local Device
    participant GMS as Google Play Services
    participant Remote as Remote Device

    Note over Local: State: CONNECTING
    Local->>GMS: requestConnection(endpointId)
    GMS->>Remote: onConnectionInitiated()
    Remote->>GMS: acceptConnection()
    GMS->>Local: onConnectionResult(SUCCESS)
    Note over Local: State: SESSION_OPEN
```

### Transfer

```mermaid
sequenceDiagram
    participant Local as Local Device
    participant Session as Control Session
    participant Remote as Remote Device

    Note over Local: State: XFER
    Local->>Session: sendBundles(selected)
    Session->>Remote: bundle payload
    Remote->>Session: HopAck (custody accepted)
    Note over Local: Bundle marked FWD
```

## Watchdog

The Nearby stack includes a watchdog that monitors connection health.

**Source:** `core/nearby/src/main/kotlin/com/meshlablite/core/nearby/watchdog/Watchdog.kt`

### Monitored Conditions

| Condition | Detection | Recovery |
|-----------|-----------|----------|
| Stale XFER | No activity for 60s | Force transition to DRAIN |
| Zombie connection | Connected but no session | Disconnect |
| Failed handshake | Sync timeout | Disconnect and backoff |
| Lost endpoint | 8011 STATUS_ENDPOINT_UNKNOWN | Clean up session |

### Error Codes

| Code | Name | Meaning |
|------|------|---------|
| 8001 | STATUS_ALREADY_DISCOVERING | Discovery already active |
| 8003 | STATUS_CONNECTION_REJECTED | Peer rejected connection |
| 8010 | STATUS_ENDPOINT_IO_ERROR | Transport failure |
| 8011 | STATUS_ENDPOINT_UNKNOWN | Endpoint no longer valid |
| 8012 | STATUS_MISSING_PERMISSION_BLE | Bluetooth permission missing |
| 8013 | STATUS_MISSING_PERMISSION_WIFI | WiFi permission missing |

## Performance Characteristics

| Metric | Typical Value |
|--------|---------------|
| Discovery time | 2-10 seconds |
| Connection time | 1-3 seconds |
| Handshake time | 100-500ms |
| Transfer speed | 50-400 Mbps (WiFi Direct) |
| BLE fallback speed | 100-500 Kbps |

## Source Files

| File | Purpose |
|------|---------|
| `core/nearby/src/.../session/SessionRegistry.kt` | Session state machine |
| `core/nearby/src/.../NearbyCore.kt` | Core transport implementation |
| `core/nearby/src/.../ConnectionCoordinator.kt` | Connection management |
| `core/nearby/src/.../DiscoveryCallbackFactory.kt` | Discovery handling |
| `core/nearby/src/.../control/ControlSession.kt` | Bundle exchange protocol |
| `core/nearby/src/.../watchdog/Watchdog.kt` | Health monitoring |

---

**Next:** [Nostr Transport](nostr.md) | [Routing Overview](../routing/overview.md)
