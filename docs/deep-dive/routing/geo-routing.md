# H3 Geo Routing

Geographic routing uses Uber's H3 hexagonal spatial index to make location-aware forwarding decisions.

## Overview

**Source:** `core/nearby/src/main/kotlin/com/meshlablite/core/nearby/routing/GeoRoutingPolicy.kt`

| Property | Value |
|----------|-------|
| **Library** | H3CoreV3 (Uber) |
| **Stale Threshold** | 6 hours |
| **Decision Types** | PREFER, AVOID, DEFAULT |

## Distance-Based Decisions

**Source:** `GeoRoutingPolicy.kt:64-104`

| Distance | Action | Reason |
|----------|--------|--------|
| ≤ 1 | **PREFER** | Neighbor matches destination |
| ≥ 4 | **AVOID** | Neighbor far from destination |
| 2-3 | **DEFAULT** | Standard routing applies |

## GeoRoutingAction Enum

**Source:** `GeoRoutingPolicy.kt:25`

```kotlin
enum class GeoRoutingAction { DEFAULT, PREFER, AVOID }
```

| Action | Effect |
|--------|--------|
| `DEFAULT` | Use PRoPHET/Spray-Wait as normal |
| `PREFER` | Priority forwarding to this neighbor |
| `AVOID` | Skip this neighbor for this bundle |

## GeoRoutingDecision

**Source:** `GeoRoutingPolicy.kt:27-34`

```kotlin
data class GeoRoutingDecision(
    val action: GeoRoutingAction,
    val reason: String? = null
) {
    companion object {
        val DEFAULT = GeoRoutingDecision(GeoRoutingAction.DEFAULT)
    }
}
```

## Stale Threshold

**Source:** `GeoRoutingPolicy.kt:113-115`

```kotlin
companion object {
    private val GEO_STALE_MS = TimeUnit.HOURS.toMillis(6)
}
```

If location data is older than 6 hours, geo routing falls back to `DEFAULT`.

## Evaluation Logic

**Source:** `GeoRoutingPolicy.kt:49-105`

```kotlin
fun evaluate(header: BundleHeader, neighborEid: String): GeoRoutingDecision {
    // 1. Get destination cell
    val dstUidHex = DeviceId(header.dstUser!!).toHex()
    val destCell = resolver.destinationSnapshot(dstUidHex)?.cell
        ?: return GeoRoutingDecision.DEFAULT

    // 2. Check staleness
    if (now - dest.updatedMs > GEO_STALE_MS) {
        return GeoRoutingDecision.DEFAULT
    }

    // 3. Get neighbor cell
    val neighborCell = resolver.neighborSnapshot(neighborEid)?.cell
        ?: return GeoRoutingDecision.DEFAULT

    // 4. Compute H3 distance
    val distance = h3.h3Distance(destCell, neighborCell)

    // 5. Make decision
    if (distance <= 1) {
        return GeoRoutingDecision(PREFER, "neighbor_matches_dest")
    }
    if (distance >= 4) {
        return GeoRoutingDecision(AVOID, "neighbor_far_from_dest")
    }
    return GeoRoutingDecision.DEFAULT
}
```

## Local Cell Optimization

**Source:** `GeoRoutingPolicy.kt:76-91`

Special case when local device is in same cell as destination:

```kotlin
val localCell = resolver.localCell()
if (localCell != null && localCell == destCell) {
    val localToNeighbor = computeDistance(localCell, neighborCell)
    if (distance >= 2 && localToNeighbor >= 3) {
        return GeoRoutingDecision(AVOID, "local_is_dest_neighbor_far")
    }
}
```

## Data Snapshots

### GeoNeighborSnapshot

**Source:** `GeoRoutingPolicy.kt:12-17`

```kotlin
data class GeoNeighborSnapshot(
    val endpointId: String,
    val deviceIdHex: String?,
    val cell: Long?,
    val updatedMs: Long?
)
```

### GeoDestinationSnapshot

**Source:** `GeoRoutingPolicy.kt:19-23`

```kotlin
data class GeoDestinationSnapshot(
    val uidHex: String,
    val cell: Long?,
    val updatedMs: Long?
)
```

## Resolver Interface

**Source:** `GeoRoutingPolicy.kt:36-40`

```kotlin
interface GeoRoutingResolver {
    fun localCell(): Long?
    fun neighborSnapshot(endpointId: String): GeoNeighborSnapshot?
    fun destinationSnapshot(uidHex: String): GeoDestinationSnapshot?
}
```

## DefaultGeoRoutingResolver

**Source:** `GeoRoutingPolicy.kt:118-150`

```kotlin
class DefaultGeoRoutingResolver(
    private val geoCellProvider: GeoCellProvider,
    private val contactDirectory: ContactDirectory,
    private val peerStore: PeerStore,
    private val peerDirectory: PeerDirectory
) : GeoRoutingResolver {
    override fun localCell(): Long? = geoCellProvider.cached()?.cell

    override fun neighborSnapshot(endpointId: String): GeoNeighborSnapshot? {
        val deviceId = peerDirectory.deviceId(endpointId)
        val entry = peerStore.getEntry(deviceId)
        return GeoNeighborSnapshot(
            endpointId = endpointId,
            deviceIdHex = deviceId,
            cell = entry?.geoCellFine,
            updatedMs = entry?.lastDiscoveryMs
        )
    }

    override fun destinationSnapshot(uidHex: String): GeoDestinationSnapshot? {
        val entry = contactDirectory.get(uidHex) ?: return null
        return GeoDestinationSnapshot(
            uidHex = uidHex,
            cell = entry.geoCellFine,
            updatedMs = entry.lastUpdatedMs
        )
    }
}
```

## H3 Distance Calculation

**Source:** `GeoRoutingPolicy.kt:107-111`

```kotlin
private fun computeDistance(a: Long, b: Long): Int? = try {
    h3?.h3Distance(a, b)
} catch (_: Exception) {
    null
}
```

**Note:** H3 distance is the number of hexagonal cells between two points at the same resolution.

## Integration with RoutingFacade

**Source:** `RoutingFacade.kt:1063-1078`

```kotlin
if (featureFlags.geoRoutingEnabled) {
    val decision = geoRoutingPolicy?.evaluate(header, neighborEid)
    if (decision?.action == GeoRoutingAction.AVOID) {
        routeSkipsCount += 1
        JsonEventLogger.emit(
            event = "ROUTE.SKIP.GEO",
            peerEid = neighborEid,
            details = mapOf(
                "bundle" to bundleId,
                "reason" to decision.reason,
                "dst_uid" to uidX
            )
        )
        return false
    }
}
```

## Feature Flag

**Source:** `RoutingFacade.kt:75`

Geo routing is controlled by a feature flag:

```kotlin
@Volatile private var featureFlags: RoutingFeatureFlags = ...
// featureFlags.geoRoutingEnabled
```

## JSON Events

| Event | When | Key Fields |
|-------|------|------------|
| `ROUTE.GEO` | Decision made | eid, action, reason, distance |
| `ROUTE.SKIP.GEO` | Bundle skipped | bundle, reason, dst_uid |

## Decision Flow

```
Bundle arrives
    ↓
Get destination cell from ContactDirectory
    ↓
Check staleness (> 6 hours?) → DEFAULT
    ↓
Get neighbor cell from PeerStore
    ↓
Check staleness (> 6 hours?) → DEFAULT
    ↓
Compute H3 distance
    ↓
distance ≤ 1? → PREFER
distance ≥ 4? → AVOID
otherwise   → DEFAULT
```

## H3 Resolution

The app uses "fine" resolution H3 cells stored in:
- `PeerStore.geoCellFine` - For neighbors
- `ContactDirectory.geoCellFine` - For destinations

See `GeoCellProvider` for resolution configuration.

## Source Files

| File | Purpose |
|------|---------|
| `GeoRoutingPolicy.kt` | Decision logic |
| `GeoCellProvider.kt` | H3 cell generation |
| `ContactDirectory.kt` | Destination locations |
| `PeerStore.kt` | Neighbor locations |
| `RoutingFacade.kt` | Integration point |

---

**Next:** [PRoPHET](prophet.md) | [ACK Learning](ack-learning.md)
