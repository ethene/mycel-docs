# Spray-and-Wait Routing

Spray-and-Wait limits message replication to prevent flooding while ensuring delivery.

## Overview

**Source:** `core/nearby/src/main/kotlin/com/meshlablite/core/nearby/routing/RoutingFacade.kt`

| Property | Value |
|----------|-------|
| **Algorithm** | Binary Spray-and-Wait |
| **Default Budget** | 3 copies |
| **Max Budget** | 8 copies |
| **Expansion Timeout** | 60 seconds |

## Copy Budget Constants

**Source:** `RoutingFacade.kt:1398-1400`

```kotlin
private companion object {
    private const val DEFAULT_COPY_BUDGET = 3
    private const val MAX_COPY_BUDGET = 8
    private const val COPY_INCREASE_TIMEOUT_MS = 60_000L
    // ...
}
```

| Constant | Value | Purpose |
|----------|-------|---------|
| `DEFAULT_COPY_BUDGET` | 3 | Initial copies per destination |
| `MAX_COPY_BUDGET` | 8 | Maximum after expansion |
| `COPY_INCREASE_TIMEOUT_MS` | 60,000 | Timeout before doubling budget |

## Per-Transport Budgets

**Source:** `RoutingFacade.kt:1329-1340`

Each transport type has independent copy limits:

```kotlin
fun getOrCreateBudget(transportId: String): TransportBudget {
    return transportBudgets.getOrPut(transportId) {
        TransportBudget(
            maxCopies = when (transportId) {
                TRANSPORT_NEARBY -> 3
                TRANSPORT_NOSTR -> 2
                TRANSPORT_RELAY -> 2
                else -> 3
            }
        )
    }
}
```

| Transport | Max Copies | Rationale |
|-----------|-----------|-----------|
| **nearby** | 3 | Direct local mesh |
| **nostr** | 2 | Per-relay limit |
| **relay** | 2 | Multi-hop via neighbor |

**Total Maximum:** 8 copies across ALL transports combined.

**Source:** `RoutingFacade.kt:1374-1379`

```kotlin
companion object {
    const val TRANSPORT_NEARBY = "nearby"
    const val TRANSPORT_NOSTR = "nostr"
    const val TRANSPORT_RELAY = "relay"
    const val TOTAL_MAX_COPIES = 8
}
```

## BundleCopyState

**Source:** `RoutingFacade.kt:1315-1380`

Tracks copy usage per bundle:

```kotlin
private data class BundleCopyState(
    var destUidHex: String,
    var maxCopies: Int,
    var shortRangeUsed: Int = 0,       // Legacy counter
    var nextExpansionMs: Long = 0L,     // Legacy backoff
    var lastUpdatedMs: Long = 0L,
    val transportBudgets: MutableMap<String, TransportBudget> = mutableMapOf()
)
```

### TransportBudget

**Source:** `RoutingFacade.kt:1299-1303`

```kotlin
private data class TransportBudget(
    var used: Int = 0,
    var maxCopies: Int = 3,
    var backoffUntilMs: Long = 0L
)
```

## Slot Reservation

**Source:** `RoutingFacade.kt:388-476`

Before sending via a transport, reserve a slot:

```kotlin
fun reserveTransportSlot(bundleId: String, destUidHex: String, transportId: String): Boolean {
    val budget = state.getOrCreateBudget(transportId)

    // Check backoff
    if (budget.backoffUntilMs > now) return false

    // Check total copies
    if (state.totalUsed() >= TOTAL_MAX_COPIES) return false

    // Check transport budget
    if (budget.used < budget.maxCopies) {
        budget.used += 1
        if (budget.used >= budget.maxCopies) {
            budget.backoffUntilMs = now + timeoutMs
        }
        return true
    }
    return false
}
```

## Copy Plan

**Source:** `RoutingFacade.kt:1287-1293`

Per-destination routing preference:

```kotlin
private data class DestinationCopyPlan(
    var preferredTransportId: String? = null,
    var preferUntilMs: Long = 0L,
    var copyBudget: Int = DEFAULT_COPY_BUDGET,
    var lastSuccessMs: Long = System.currentTimeMillis(),
    var lastUpdatedMs: Long = System.currentTimeMillis()
)
```

### Copy Plan Update on ACK

**Source:** `RoutingFacade.kt:1108-1121`

When delivery is confirmed:

```kotlin
private fun updateCopyPlan(destUid: String, record: AckTelemetryRecord) {
    plan.preferredTransportId = preferredTransport
    plan.lastSuccessMs = record.deliveredAtMs
    plan.preferUntilMs = record.deliveredAtMs + computeWindowMs(latency)
    plan.copyBudget = (plan.copyBudget - 1).coerceAtLeast(1)
    releaseBundleState(record.bundleId)
}
```

### Copy Plan Expansion

**Source:** `RoutingFacade.kt:1123-1132`

When delivery fails (timeout):

```kotlin
private fun maybeExpandCopyPlan(destUid: String) {
    if (now > plan.preferUntilMs && now - plan.lastSuccessMs > COPY_INCREASE_TIMEOUT_MS) {
        plan.copyBudget = (plan.copyBudget * 2).coerceAtMost(MAX_COPY_BUDGET)
        plan.preferredTransportId = null
        plan.preferUntilMs = 0L
    }
}
```

## Transport Independence

**Critical Design:** One transport being exhausted does NOT block others.

**Source:** `RoutingFacade.kt:340-377`

```kotlin
fun copyPlanBackoffMillis(bundleId: String, transportId: String? = null): Long? {
    // If transportId specified, check that specific transport
    if (transportId != null) {
        return state.getTransportBackoffMs(transportId, now)
    }

    // Only block if ALL transports are exhausted
    if (state.totalUsed() < TOTAL_MAX_COPIES) {
        val allTransportsInBackoff = listOf(
            TRANSPORT_NEARBY, TRANSPORT_RELAY, TRANSPORT_NOSTR
        ).all { tid -> state.isTransportInBackoff(tid, now) }

        if (!allTransportsInBackoff) return null  // At least one available
    }
    // ...
}
```

## Bundle Categories

**Source:** `RoutingFacade.kt:1238-1254`

Bundles are classified for routing decisions:

```kotlin
private enum class BundleCategory {
    DM,           // Direct message to specific user
    SUBSCRIPTION, // Topic/channel message
    MESH,         // Mesh-local broadcast
    OTHER         // Everything else
}
```

| Category | Routing Strategy |
|----------|------------------|
| **DM** | PRoPHET + Spray-Wait + Geo |
| **SUBSCRIPTION** | Interest-aware + Spray |
| **MESH** | Always forward (mesh_local hint) |
| **OTHER** | Default forward |

## Timeout Constants

**Source:** `RoutingFacade.kt:1408-1411`

```kotlin
private const val SHORT_RANGE_TIMEOUT_MULTIPLIER = 3.5
private const val MIN_SHORT_RANGE_TIMEOUT_MS = 10_000L
private const val MAX_SHORT_RANGE_TIMEOUT_MS = 300_000L
```

| Constant | Value | Purpose |
|----------|-------|---------|
| Multiplier | 3.5× | Timeout = latency × 3.5 |
| Minimum | 10 seconds | Floor for fast paths |
| Maximum | 5 minutes | Ceiling for slow paths |

## Slot Release on Timeout

**Source:** `RoutingFacade.kt:488-526`

```kotlin
fun releaseTransportSlot(bundleId: String, destUidHex: String?, transportId: String): Boolean {
    if (budget.used > 0) {
        budget.used -= 1
        // Apply route penalty
        destUidHex?.let { dest ->
            routingTable.applyRoutePenalty(dest)
        }
        return true
    }
    return false
}
```

## JSON Events

| Event | When | Key Fields |
|-------|------|------------|
| `ROUTE.SLOT.BACKOFF` | Transport in backoff | bundle, transport, backoff_remaining_ms |
| `ROUTE.SLOT.TOTAL_EXHAUSTED` | All copies used | bundle, total_used, max |
| `ROUTE.SLOT.TRANSPORT_EXHAUSTED` | Transport budget used | bundle, transport, used, max |
| `ROUTE.SLOT.TIMEOUT_RELEASE` | Slot freed on timeout | bundle, transport, penalty_p |
| `ROUTE.SKIP.COPY_PLAN` | Send skipped | bundle, reason, transport |

## Diagnostics

Via meshctl:

```bash
meshctl --serial <SERIAL> routing copy-plans

# Output:
# {
#   "destinations": 5,
#   "pending_bundles": 3,
#   "prefer_short_range": 4,
#   "long_haul_preferred": 1,
#   "backoff_bundles": 0,
#   "samples": [...]
# }
```

## Source Files

| File | Purpose |
|------|---------|
| `RoutingFacade.kt` | Spray-Wait implementation |
| `BundleRepository.kt` | Bundle scheduling |
| `TransferOrchestrator.kt` | Slot consumption |

---

**Next:** [Geo Routing](geo-routing.md) | [PRoPHET](prophet.md)
