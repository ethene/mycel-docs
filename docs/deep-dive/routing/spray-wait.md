# Spray-and-Wait Routing

Spray-and-Wait limits message replication to prevent flooding while ensuring delivery.

## Overview

| Property | Value |
|----------|-------|
| **Algorithm** | Binary Spray-and-Wait |
| **Default Budget** | 3 copies |
| **Max Budget** | 8 copies |
| **Expansion Timeout** | 60 seconds |

## Copy Budget Constants

| Constant | Value | Purpose |
|----------|-------|---------|
| `DEFAULT_COPY_BUDGET` | 3 | Initial copies per destination |
| `MAX_COPY_BUDGET` | 8 | Maximum after expansion |
| `COPY_INCREASE_TIMEOUT_MS` | 60,000 | Timeout before doubling budget |

## Per-Transport Budgets

Each transport type has independent copy limits:

| Transport | Max Copies | Rationale |
|-----------|-----------|-----------|
| **nearby** | 3 | Direct local mesh |
| **nostr** | 2 | Per-relay limit |
| **relay** | 2 | Multi-hop via neighbor |

**Total Maximum:** 8 copies across ALL transports combined.

## BundleCopyState

Tracks copy usage per bundle with the following fields:
- **destUidHex** - Destination user ID
- **maxCopies** - Maximum allowed copies
- **shortRangeUsed** - Legacy counter for backward compatibility
- **nextExpansionMs** - Legacy backoff timing
- **lastUpdatedMs** - Last state update time
- **transportBudgets** - Per-transport budget tracking

### TransportBudget

Each transport maintains:
- **used** - Number of copies sent via this transport
- **maxCopies** - Maximum for this transport (default: 3)
- **backoffUntilMs** - Backoff expiration time

## Slot Reservation

Before sending via a transport, a slot must be reserved. The reservation checks:
1. Is the transport in backoff? → Skip
2. Are total copies exhausted? → Skip
3. Is the transport budget available? → Reserve and send

When a transport reaches its limit, it enters backoff until the timeout expires.

## Copy Plan

Per-destination routing preference that tracks:
- **preferredTransportId** - Best transport for this destination
- **preferUntilMs** - Preference expiration time
- **copyBudget** - Current budget for this destination
- **lastSuccessMs** - Time of last successful delivery
- **lastUpdatedMs** - Last plan update time

### Copy Plan Update on ACK

When delivery is confirmed:
1. Set preferred transport based on successful path
2. Update last success time
3. Reduce copy budget (optimize for known-good paths)
4. Release bundle state

### Copy Plan Expansion

When delivery fails (timeout):
1. Double the copy budget (up to maximum)
2. Clear preferred transport
3. Try all available paths

## Transport Independence

**Critical Design:** One transport being exhausted does NOT block others.

If the "nearby" budget is full, messages can still be sent via "nostr" or "relay". Only when ALL transports are exhausted or in backoff is sending blocked.

## Bundle Categories

Bundles are classified for routing decisions:

| Category | Routing Strategy |
|----------|------------------|
| **DM** | PRoPHET + Spray-Wait + Geo |
| **SUBSCRIPTION** | Interest-aware + Spray |
| **MESH** | Always forward (mesh_local hint) |
| **OTHER** | Default forward |

## Timeout Constants

| Constant | Value | Purpose |
|----------|-------|---------|
| Multiplier | 3.5× | Timeout = latency × 3.5 |
| Minimum | 10 seconds | Floor for fast paths |
| Maximum | 5 minutes | Ceiling for slow paths |

## Slot Release on Timeout

When a transport slot times out:
1. Decrement the used counter
2. Apply route penalty to PRoPHET table
3. Log the timeout event

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
```
meshctl --serial <SERIAL> routing copy-plans
```

Shows destination count, pending bundles, transport preferences, and sample entries.

---

**Next:** [Geo Routing](geo-routing.md) | [PRoPHET](prophet.md)
