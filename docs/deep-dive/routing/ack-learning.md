# ACK-Based Path Learning

ACK (acknowledgment) messages are used not just for UI delivery confirmation, but as telemetry for routing optimization.

## Overview

| Property | Value |
|----------|-------|
| **Purpose** | Learn optimal paths from delivery confirmations |
| **Timeout Check** | Every 30 seconds |
| **Path Tracking** | Per-destination, per-transport |

## Timeout Constants

| Transport | Timeout | Rationale |
|-----------|---------|-----------|
| **Nearby** | 30 seconds | Local mesh, fast RTT expected |
| **Default** | 60 seconds | General fallback |
| **Nostr** | 120 seconds | Higher latency via relays |

## Core Responsibilities

1. **Track sent bundles** - Record which bundles are awaiting ACK
2. **Detect timeouts** - Identify bundles without ACK within window
3. **Record failures** - Update path tracking on timeout
4. **Suggest alternates** - Recommend different path when primary fails

## Data Structures

### PendingAck

Each pending ACK tracks:
- **bundleId** - The bundle awaiting confirmation
- **destUidHex** - Destination user ID
- **transportId** - Transport used for sending
- **sentAtMs** - Timestamp when sent

## Lifecycle

### On Bundle Sent

When a bundle is sent via any transport, a pending ACK entry is created with the bundle ID, destination, transport, and current timestamp.

### On ACK Received

When an ACK arrives:
1. Remove from pending map
2. Log path learning event
3. Update transport path tracking with success

### Timeout Check

Every 30 seconds, the pending ACK map is checked:
1. Find all entries older than the timeout threshold
2. Remove from pending map
3. Record timeout in path tracker
4. Apply route penalty to PRoPHET table
5. Suggest alternate transport for retry

## Path Suggestion Logic

When a timeout occurs:
1. Get the best known path for the destination
2. If different from the failed transport → suggest it
3. Otherwise, find any non-degraded alternate path

## Integration with Routing

The ACK path learner is integrated with the routing facade:
- **On timeout** → Route penalty callback is invoked
- **On success** → Preferred transport is recorded
- **On query** → Best transport is suggested

## Periodic Timeout Job

A background job runs every 30 seconds to:
1. Check all pending ACKs for timeouts
2. Process timeouts and record path failures
3. Emit telemetry events for monitoring

## Debug Interface

Access via meshctl:
```
meshctl --serial <SERIAL> routing metrics
```

Shows:
- Pending ACK count
- Current timeout thresholds
- Sample pending entries
- Path metrics summary

## JSON Events

| Event | When | Key Fields |
|-------|------|------------|
| `ACK.PATH.LEARN` | ACK received | bundle, dest, transport, rtt_ms |
| `ACK.TIMEOUT` | Timeout detected | bundle, failed_transport, suggested_transport |
| `ACK.TIMEOUT.BATCH` | Batch processed | count, remaining_pending |
| `ROUTE.PENALTY` | Penalty applied | dest, old_p, new_p, reason |

## Best Transport Selection

When selecting a transport for a destination:
1. Query the path tracker for the best known path
2. Consider recent successes and failures
3. Fall back to default if no history exists

---

**Next:** [Spray-and-Wait](spray-wait.md) | [Geo Routing](geo-routing.md)
