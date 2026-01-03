# Nostr Transport

Nostr is a secondary transport using WebSocket-connected relays for long-range message delivery.

## Overview

| Property | Value |
|----------|-------|
| **Protocol** | Nostr (NIP-01 base + NIP-17/44 encryption) |
| **Connection** | WebSocket to relay servers |
| **Range** | Global (internet required) |
| **Latency** | Seconds to minutes |
| **Bandwidth** | Limited by relay policies |

## Architecture

```mermaid
flowchart TB
    subgraph Device
        NTA[NostrTransportAdapter]
        RP[RelayPool]
        GW[GiftWrap]
    end

    subgraph Relays
        R1[wss://relay1.example]
        R2[wss://relay2.example]
        R3[wss://relay3.example]
    end

    NTA --> RP
    RP --> R1
    RP --> R2
    RP --> R3
    NTA --> GW
```

## Relay Connection States

```mermaid
stateDiagram-v2
    [*] --> Connecting : start()
    Connecting --> Connected : onOpen
    Connected --> Closing : onClosing
    Closing --> Closed : onClosed
    Connected --> Failed : onFailure
    Failed --> Connecting : auto-reconnect
```

| State | Description |
|-------|-------------|
| `Connecting` | WebSocket being opened |
| `Connected` | WebSocket open, usable |
| `Closing` | Remote initiated close |
| `Closed` | Connection closed |
| `Failed` | Error, triggers reconnect |

## Default Relays

Default relay servers:
- `wss://relay.damus.io`
- `wss://nos.lol`
- `wss://relay.nostr.band`

Users can configure custom relays via MeshConfig.

## Gift-Wrap Encryption (NIP-17)

To hide sender/recipient from relays, messages are double-encrypted.

### Encryption Layers

1. **Inner (Seal):** Message encrypted to recipient using NIP-44
2. **Outer (Gift-Wrap):** Sealed event encrypted to recipient with random author

```mermaid
flowchart TB
    subgraph Original
        M[Bundle Payload]
    end

    subgraph Seal["Sealed Event"]
        S1[Kind: 13]
        S2[Content: NIP-44 encrypted payload]
        S3[Tags: recipient pubkey]
    end

    subgraph GiftWrap["Gift-Wrapped Event"]
        G1[Kind: 1059]
        G2[Content: NIP-44 encrypted seal]
        G3[Pubkey: random]
        G4[Created_at: randomized ±48h]
    end

    M --> S2
    S1 --> G2
    S2 --> G2
    S3 --> G2
```

### Security Properties

| Property | How Achieved |
|----------|--------------|
| Sender hidden | Random pubkey as gift-wrap author |
| Recipient hidden | Encrypted to recipient, no plaintext tag |
| Timing hidden | Randomized timestamp ±48 hours |
| Content hidden | Double NIP-44 encryption |

## Event Flow

### Sending

```mermaid
sequenceDiagram
    participant App as Application
    participant NTA as NostrTransportAdapter
    participant GW as GiftWrap
    participant Relay as Relay

    App->>NTA: send(bundle, recipient)
    NTA->>GW: wrapBundle(bundle)
    GW->>GW: Create sealed event
    GW->>GW: Gift-wrap sealed event
    GW-->>NTA: gift-wrapped event
    NTA->>Relay: EVENT (kind 1059)
    Relay-->>NTA: OK
```

### Receiving

```mermaid
sequenceDiagram
    participant Relay as Relay
    participant NTA as NostrTransportAdapter
    participant GW as GiftWrap
    participant App as Application

    Relay->>NTA: EVENT (kind 1059)
    NTA->>GW: unwrap(event)
    GW->>GW: Decrypt gift-wrap
    GW->>GW: Decrypt seal
    GW-->>NTA: bundle payload
    NTA->>App: onBundleReceived(bundle)
```

## Subscription

The adapter subscribes to gift-wrapped events (kind 1059) for the local device's public key, filtering from the last sync timestamp.

## Transport Status

| Status | Meaning |
|--------|---------|
| `DOWN` | No relays connected |
| `UP` | At least one relay connected |
| `DEGRADED` | Some relays failing |

Status is computed based on the number of connected relays compared to the desired relay count.

## Backoff and Retry

Failed connections use exponential backoff with a maximum delay of 5 minutes.

| Attempt | Delay |
|---------|-------|
| 1 | 1s |
| 2 | 2s |
| 3 | 4s |
| 4 | 8s |
| 5+ | 5min (max) |

## Configuration

Nostr transport can be enabled/disabled and relay URLs can be configured.

Toggle via meshctl:
- `meshctl config set nostr_enabled true`
- `meshctl config set nostr_relays "wss://relay1.example,wss://relay2.example"`

## Comparison to Nearby

| Aspect | Nearby | Nostr |
|--------|--------|-------|
| Range | ~100m | Global |
| Latency | <1s | Seconds-minutes |
| Privacy | Local only | Relay sees encrypted events |
| Power | Higher (radio) | Lower (WiFi) |
| Reliability | Varies by proximity | Varies by relay |
| Cost | Free | Free (public relays) |

## When Nostr is Used

1. **No Nearby peers:** Nostr is fallback when no local peers available
2. **ACK-based skip:** After ACK via Nearby, skip Nostr copies
3. **Parallel delivery:** Both transports may be used simultaneously

---

**Next:** [Nearby Transport](nearby.md) | [Routing Overview](../routing/overview.md)
