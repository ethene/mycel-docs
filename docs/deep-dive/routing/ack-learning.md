# ACK-Based Path Learning

ACK (acknowledgment) messages are used not just for UI delivery confirmation, but as telemetry for routing optimization.

## Overview

**Source:** `core/nearby/src/main/kotlin/com/meshlablite/core/nearby/routing/AckPathLearner.kt`

| Property | Value |
|----------|-------|
| **Purpose** | Learn optimal paths from delivery confirmations |
| **Timeout Check** | Every 30 seconds |
| **Path Tracking** | Per-destination, per-transport |

## Timeout Constants

**Source:** `AckPathLearner.kt:201-209`

```kotlin
companion object {
    /** Default ACK timeout: 60 seconds */
    const val DEFAULT_ACK_TIMEOUT_MS = 60_000L

    /** Aggressive timeout for nearby: 30 seconds */
    const val NEARBY_ACK_TIMEOUT_MS = 30_000L

    /** Lenient timeout for Nostr: 120 seconds */
    const val NOSTR_ACK_TIMEOUT_MS = 120_000L
}
```

| Transport | Timeout | Rationale |
|-----------|---------|-----------|
| **Nearby** | 30 seconds | Local mesh, fast RTT expected |
| **Default** | 60 seconds | General fallback |
| **Nostr** | 120 seconds | Higher latency via relays |

## Core Responsibilities

**Source:** `AckPathLearner.kt:6-14`

1. **Track sent bundles** - Record which bundles are awaiting ACK
2. **Detect timeouts** - Identify bundles without ACK within window
3. **Record failures** - Update TransportPathTracker on timeout
4. **Suggest alternates** - Recommend different path when primary fails

## Data Structures

### PendingAck

**Source:** `AckPathLearner.kt:25-30`

```kotlin
private data class PendingAck(
    val bundleId: String,
    val destUidHex: String,
    val transportId: String,
    val sentAtMs: Long
)
```

## Lifecycle

### On Bundle Sent

**Source:** `AckPathLearner.kt:38-46`

```kotlin
fun onBundleSent(bundleId: String, destUidHex: String, transportId: String) {
    val pending = PendingAck(
        bundleId = bundleId,
        destUidHex = destUidHex.lowercase(),
        transportId = transportId,
        sentAtMs = System.currentTimeMillis()
    )
    pendingAcks[bundleId] = pending
}
```

### On ACK Received

**Source:** `AckPathLearner.kt:52-70`

When ACK arrives:
1. Remove from pending map
2. Log path learning event
3. TransportPathTracker updated by RoutingFacade

### Timeout Check

**Source:** `AckPathLearner.kt:78-135`

Called every 30 seconds by `RoutingFacade.ensureTimeoutCheck()`:

```kotlin
fun checkTimeouts(): Int {
    val now = System.currentTimeMillis()
    val timedOut = pendingAcks.entries.filter { (_, pending) ->
        now - pending.sentAtMs > ackTimeoutMs
    }

    for ((bundleId, pending) in timedOut) {
        pendingAcks.remove(bundleId)

        // Record timeout in path tracker
        pathTracker.recordTimeout(pending.destUidHex, pending.transportId)

        // Apply route penalty
        onRoutePenalty?.invoke(pending.destUidHex)

        // Find best alternate path
        val suggestedTransport = pathTracker.getBestPath(pending.destUidHex)

        // Notify for retry
        onRetryNeeded?.invoke(bundleId, pending.destUidHex, suggestedTransport)
    }
    return timedOut.size
}
```

## Path Suggestion Logic

**Source:** `AckPathLearner.kt:97-107`

When a timeout occurs, the learner suggests an alternate transport:

1. Get best path for destination
2. If different from failed transport → suggest it
3. Otherwise, find any non-degraded path

```kotlin
val suggestedTransport = if (bestPath != pending.transportId) {
    bestPath  // Different path available
} else {
    // Find any non-degraded alternate
    pathTracker.getPathsRanked(pending.destUidHex)
        .filter { !it.isDegraded && it.transportId != pending.transportId }
        .firstOrNull()
        ?.transportId
}
```

## Integration with RoutingFacade

**Source:** `RoutingFacade.kt:96-114`

The `AckPathLearner` is instantiated with a route penalty callback:

```kotlin
val ackPathLearner: AckPathLearner = AckPathLearner(
    pathTracker = pathTracker,
    onRoutePenalty = { destUidHex ->
        // Apply penalty to PRoPHET routing table
        val newP = routingTable.applyRoutePenalty(destUidHex)
        // Log the penalty
    }
)
```

## Periodic Timeout Job

**Source:** `RoutingFacade.kt:667-685`

```kotlin
fun ensureTimeoutCheck() {
    if (timeoutCheckJob?.isActive == true) return
    timeoutCheckJob = scope.launch {
        while (true) {
            delay(30_000)  // Check every 30 seconds
            val count = checkAckTimeouts()
            if (count > 0) {
                JsonEventLogger.emit(
                    event = "ROUTE.TIMEOUT.CHECK",
                    details = mapOf("timeouts_processed" to count)
                )
            }
        }
    }
}
```

## Debug Interface

**Source:** `AckPathLearner.kt:167-185`

```kotlin
fun getDebugState(): Map<String, Any?> {
    return mapOf(
        "pending_count" to pendingAcks.size,
        "ack_timeout_ms" to ackTimeoutMs,
        "samples" to pendingAcks.entries.take(10).map { ... },
        "path_metrics" to pathTracker.getMetricsSummary()
    )
}
```

Access via meshctl:
```bash
meshctl --serial <SERIAL> routing metrics
# Shows ack_learner state when pending > 0
```

## JSON Events

| Event | When | Key Fields |
|-------|------|------------|
| `ACK.PATH.LEARN` | ACK received | bundle, dest, transport, rtt_ms |
| `ACK.TIMEOUT` | Timeout detected | bundle, failed_transport, suggested_transport |
| `ACK.TIMEOUT.BATCH` | Batch processed | count, remaining_pending |
| `ROUTE.PENALTY` | Penalty applied | dest, old_p, new_p, reason |

## Best Transport Selection

**Source:** `AckPathLearner.kt:141-143`

```kotlin
fun getBestTransport(destUidHex: String): String? {
    return pathTracker.getBestPath(destUidHex.lowercase())
}
```

Used by RoutingFacade to make transport decisions:

**Source:** `RoutingFacade.kt:700-702`

```kotlin
fun getBestTransportForDestination(destUidHex: String): String? {
    return ackPathLearner.getBestTransport(destUidHex)
}
```

## Source Files

| File | Purpose |
|------|---------|
| `AckPathLearner.kt` | Timeout tracking, path learning |
| `TransportPathTracker.kt` | Path metrics storage |
| `RoutingFacade.kt` | Integration, periodic checks |
| `ControlMsg.kt` | DeliveryAck message type |

---

**Next:** [Spray-and-Wait](spray-wait.md) | [Geo Routing](geo-routing.md)
