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

    classDef appLayer fill:#64B5F6,stroke:#1565C0,color:#000
    classDef dtnLayer fill:#81C784,stroke:#2E7D32,color:#000
    classDef transportLayer fill:#FFB74D,stroke:#EF6C00,color:#000

    class UI,VM,SVC appLayer
    class REPO,ROUTE,ACK,CRDT dtnLayer
    class REG,NEARBY,NOSTR transportLayer
```

## Layer Responsibilities

### Application Layer

| Component | Purpose |
|-----------|---------|
| **UI Screens** | Jetpack Compose screens for chat, contacts, settings |
| **ViewModels** | State management, UI logic |
| **MeshService** | Foreground service, wake locks, lifecycle |

The Application layer is purely UI-focused. It never handles raw bundles or transport connections directly.

### DTN Layer

| Component | Purpose |
|-----------|---------|
| **BundleRepository** | Bundle storage, status tracking, TTL management |
| **RoutingFacade** | PRoPHET + Spray-and-Wait routing decisions |
| **AckPathLearner** | ACK processing, route improvement |
| **MembershipCrdt** | 2P-Set CRDT for group membership sync |

The DTN layer handles message storage, routing decisions, and delivery tracking. It is transport-agnostic.

### Transport Layer

| Component | Purpose |
|-----------|---------|
| **TransportRegistry** | Unified API for all transports |
| **Nearby Connections** | BLE + WiFi Direct via Google Play Services |
| **Nostr Transport** | WebSocket relay pool with NIP-17/44 encryption |

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

---

**Next:** [Transports - Nearby](../transports/nearby.md) | [Routing Overview](../routing/overview.md)
