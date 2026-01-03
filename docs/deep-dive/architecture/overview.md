# Architecture Overview

Mycel uses a three-layer architecture that separates concerns between user interface, message handling, and physical transport.

## Three-Layer Stack

```mermaid
flowchart TB
    subgraph APP ["Application Layer"]
        direction LR
        UI["Jetpack Compose UI<br/>13 screens"]
        VM["ViewModels<br/>Chat, Settings, etc."]
        SVC["MeshService<br/>Foreground Service"]
    end

    subgraph DTN ["DTN Layer"]
        direction LR
        REPO["BundleRepository<br/>Store & Forward"]
        ROUTE["RoutingFacade<br/>PRoPHET + Spray"]
        ACK["AckPathLearner<br/>Delivery Tracking"]
        CRDT["MembershipCrdt<br/>Group Sync"]
    end

    subgraph TRANSPORT ["Transport Layer"]
        direction LR
        REG["TransportRegistry<br/>Unified API"]
        NEARBY["Nearby Connections<br/>BLE + WiFi"]
        NOSTR["Nostr Transport<br/>Relay Pool"]
    end

    UI --> VM
    VM --> SVC
    SVC --> REPO
    REPO --> ROUTE
    ROUTE --> ACK
    ROUTE --> CRDT

    REPO --> REG
    REG --> NEARBY
    REG --> NOSTR

    classDef appLayer fill:#E3F2FD,stroke:#1976D2
    classDef dtnLayer fill:#E8F5E9,stroke:#388E3C
    classDef transportLayer fill:#FFF3E0,stroke:#F57C00

    class UI,VM,SVC appLayer
    class REPO,ROUTE,ACK,CRDT dtnLayer
    class REG,NEARBY,NOSTR transportLayer
```

## Layer Responsibilities

### Application Layer

| Component | Purpose | Source |
|-----------|---------|--------|
| **UI Screens** | Jetpack Compose screens for chat, contacts, settings | `feature/ui/src/.../screens/` |
| **ViewModels** | State management, UI logic | `feature/ui/src/.../viewmodels/` |
| **MeshService** | Foreground service, wake locks, lifecycle | `app/src/.../service/MeshService.kt` |

The Application layer is purely UI-focused. It never handles raw bundles or transport connections directly.

### DTN Layer

| Component | Purpose | Source |
|-----------|---------|--------|
| **BundleRepository** | Bundle storage, status tracking, TTL management | `core/dtn/.../BundleRepository.kt` |
| **RoutingFacade** | PRoPHET + Spray-and-Wait routing decisions | `core/nearby/.../RoutingFacade.kt` |
| **AckPathLearner** | ACK processing, route improvement | `core/nearby/.../AckPathLearner.kt` |
| **MembershipCrdt** | 2P-Set CRDT for group membership sync | `core/dtn/.../MembershipCrdt.kt` |

The DTN layer handles message storage, routing decisions, and delivery tracking. It is transport-agnostic.

### Transport Layer

| Component | Purpose | Source |
|-----------|---------|--------|
| **TransportRegistry** | Unified API for all transports | `core/transport/.../TransportRegistry.kt` |
| **Nearby Connections** | BLE + WiFi Direct via Google Play Services | `core/nearby/` |
| **Nostr Transport** | WebSocket relay pool with NIP-17/44 encryption | `core/transport/.../nostr/` |

The Transport layer handles physical message delivery. Multiple transports form one unified network.

## Key Design Principles

### 1. Transport Agnosticism
The DTN layer does not know or care which transport delivered a bundle. All transports look the same to the routing layer.

### 2. Store and Forward
Every node stores received bundles locally and forwards them opportunistically. There is no requirement for end-to-end connectivity.

### 3. Peer Equality
All nodes are equal. There are no designated servers, coordinators, or privileged nodes.

### 4. Local-First
All operations work offline. Network connectivity improves delivery speed but is never required.

## Data Flow Example

A DM from Alice to Bob when they're not directly connected:

```mermaid
sequenceDiagram
    participant Alice as Alice
    participant A_DTN as Alice DTN
    participant Charlie as Charlie (relay)
    participant C_DTN as Charlie DTN
    participant B_DTN as Bob DTN
    participant Bob as Bob

    Alice->>A_DTN: Send DM to Bob
    A_DTN->>A_DTN: Create bundle (NEW)
    A_DTN->>A_DTN: Encrypt payload (ECIES)

    Note over A_DTN,Charlie: Later: Alice meets Charlie

    A_DTN->>Charlie: Forward bundle (Nearby)
    Charlie->>C_DTN: Store bundle (RECEIVED_FORWARDABLE)

    Note over C_DTN,B_DTN: Later: Charlie meets Bob

    C_DTN->>B_DTN: Forward bundle (Nearby)
    B_DTN->>B_DTN: Decrypt payload
    B_DTN->>Bob: Deliver message
    B_DTN->>C_DTN: Send DeliveryAck
    C_DTN->>A_DTN: Relay DeliveryAck
```

## Source Files

| File | Purpose | Lines |
|------|---------|-------|
| `core/dtn/src/.../BundleRepository.kt` | Bundle persistence and status | ~1800 |
| `core/nearby/src/.../RoutingFacade.kt` | Routing algorithm implementation | ~700 |
| `core/nearby/src/.../NearbyCore.kt` | Nearby transport core | ~3500 |
| `core/transport/src/.../NostrTransportAdapter.kt` | Nostr relay pool | ~400 |
| `app/src/.../service/MeshService.kt` | Foreground service | ~300 |

---

**Next:** [Transports - Nearby](../transports/nearby.md) | [Routing Overview](../routing/overview.md)
