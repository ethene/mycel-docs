# Crypto & Identity Integration (Planned)

This page documents research on crypto and identity integrations suitable for Mycel's delay-tolerant mesh network.

!!! info "Status: Researched - Implementation Planned"
    Research completed January 2026. Integration priorities established.

---

## Overview

Mycel's DTN architecture creates unique constraints for crypto integration:

- **Days-scale latency** - Transactions may not confirm for hours/days
- **No global consensus** - Can't rely on real-time blockchain access
- **Intermittent connectivity** - Must work offline, sync opportunistically

This research identifies which technologies work in these conditions.

---

## Nostr NIP Compatibility

Mycel already uses Nostr (NIP-17 for DMs). Here's how other NIPs fit DTN:

| NIP | Description | DTN Compatible | Notes |
|-----|-------------|----------------|-------|
| **NIP-17** | Gift-wrapped DMs | ✅ Yes | Already implemented |
| **NIP-77** | Negentropy sync | ✅ Yes | Efficient mesh sync - peers exchange only missing data |
| **NIP-BE** | BLE transport | ✅ Yes | Defines Nostr-over-Bluetooth, perfect for mesh |
| **NIP-60** | Cashu e-cash | ✅ Yes | Offline bearer tokens |
| **NIP-05** | DNS identity | ⚠️ Partial | Cache when online, can't resolve offline |
| **NIP-15** | Marketplace | ⚠️ Partial | Listings work, payments need settlement |
| **NIP-47** | Wallet Connect | ⚠️ Partial | Coordination offline, execution deferred |
| **NIP-57** | Lightning Zaps | ⚠️ Partial | Requires LN connectivity |
| **NIP-65/66** | Relay discovery | ⚠️ Partial | Useful for gateway behavior |

### High Priority NIPs

**NIP-77 (Negentropy):** Enables efficient sync between peers. When two Mycel nodes meet after a gap, they exchange summary hashes and transfer only missing messages. Critical for bandwidth-constrained mesh.

**NIP-BE (BLE Transport):** Defines how to run Nostr over Bluetooth. Adopting this means interoperability with other Nostr apps using BLE. Devices can auto-sync messages via BLE advertisements.

---

## Crypto Payment Options

### What Works Offline

| Technology | Offline? | How It Works |
|------------|----------|--------------|
| **Cashu e-cash** | ✅ Yes | Bearer tokens transferred P2P, mint verifies on redemption |
| **On-chain Bitcoin** | ⚠️ Partial | Sign offline, carry TX until gateway broadcasts |
| **Lightning (local)** | ⚠️ Partial | Works within connected mesh graph |
| **Fedimint** | ⚠️ Partial | Community-run mints can operate locally |

### What Doesn't Work

| Technology | Why Not |
|------------|---------|
| MobileCoin | Requires online consensus nodes |
| Stablecoins (USDT/USDC) | Blockchain access required for every transfer |
| Standard Lightning | Needs path to destination, online routing |
| Any coin requiring mempool | Can't verify without network access |

---

## Recommended: Cashu E-cash

Cashu is the standout solution for offline payments in Mycel.

### How It Works

```
┌─────────────┐     Deposit BTC      ┌──────────────┐
│    User     │ ───────────────────► │  Cashu Mint  │
│  (online)   │ ◄─────────────────── │   (online)   │
└─────────────┘     Receive tokens   └──────────────┘
       │
       │ Later, offline...
       ▼
┌─────────────┐     Send tokens      ┌─────────────┐
│   Sender    │ ───────────────────► │  Recipient  │
│  (offline)  │   via BLE/QR/chat    │  (offline)  │
└─────────────┘                      └─────────────┘
       │                                    │
       │                                    │ When online...
       │                                    ▼
       │                             ┌──────────────┐
       │                             │  Cashu Mint  │
       │                             │   (online)   │
       └──────────────────────────── │ Redeem/verify│
                                     └──────────────┘
```

### Why Cashu for DTN

| Property | Benefit for Mycel |
|----------|-------------------|
| **Bearer tokens** | Transfer without mint involvement |
| **Offline sending** | Works over mesh, QR, text |
| **Offline receiving** | Recipient verifies later |
| **Double-spend prevention** | Mint rejects duplicate redemptions |
| **Privacy** | Blind signatures hide transaction graph |

### Proven in Practice

BitChat (by Jack Dorsey's team) demonstrated Cashu over Bluetooth:
- Sent BTC phone-to-phone with no internet
- Receiving phone redeemed tokens when later online
- Sender didn't need to be online for redemption

### Trade-offs

| Concern | Mitigation |
|---------|------------|
| Mint trust | Use federated mints (Fedimint) or reputable operators |
| Mint offline | Multiple mint support, fallback options |
| Token validity | Show "unverified" status until mint confirms |

---

## Lightning Network (Conditional)

Lightning can work in a local mesh under specific conditions.

### When It Works

- Both parties have open channels
- Channel graph is connected within mesh
- Someone has watchtower access (or short offline periods)

Research showed ~95% payment success in well-connected local mesh scenarios.

### When It Fails

| Scenario | Problem |
|----------|---------|
| Long offline periods | Counterparty can broadcast old state, steal funds |
| No channel path | Payment can't route |
| Expired HTLCs | Time-sensitive payments fail |

### Recommendation

Offer Lightning as **opt-in power feature** for communities that can manage it:
- Implement watchtower support
- Use long channel timelocks
- Warn users about offline risks

For general users, prefer Cashu (simpler, safer offline).

---

## Decentralized Identity (DIDs)

DIDs and Verifiable Credentials work well offline.

### Peer DIDs (did:peer)

- Generated locally, no blockchain needed
- Exchange directly between devices
- Perfect for mesh identity

Mycel already uses Ed25519 keypairs - these can be represented as `did:key` DIDs.

### Verifiable Credentials

Use cases in DTN context:

| Credential | Example |
|------------|---------|
| Membership | "Member of Community X" |
| Role | "Certified Medic" |
| Authorization | "Can access emergency channel" |

**How it works:**
1. Issuer signs credential offline (JSON + signature)
2. User stores in Mycel profile
3. Verifier checks signature locally (has issuer's pubkey)
4. No internet required for verification

### Implementation Notes

- Revocation needs careful handling (sync revocation lists via mesh)
- Short-lived credentials avoid revocation complexity
- Cache issuer pubkeys for offline verification

---

## Anti-Patterns to Avoid

What NOT to do when adding crypto to DTN:

| Anti-Pattern | Why It Fails |
|--------------|--------------|
| **Always-online servers** | Server unreachable = feature broken |
| **Instant consensus** | No global state available offline |
| **Short timeouts** | DTN latency exceeds typical crypto timeouts |
| **Unverified acceptance** | Accepting payment without verification risks fraud |
| **DNS/PKI dependency** | Can't resolve offline |
| **Broadcast flooding** | Exhausts limited mesh bandwidth |
| **Skipping signatures** | Mesh is untrusted, MITM possible |

### Design Principles

1. **Eventual consistency** - Show pending states, verify later
2. **Local trust anchors** - Cache keys, don't require lookups
3. **Patience built-in** - Days-scale delivery is normal
4. **Graceful degradation** - Warn but don't block when offline

---

## Implementation Roadmap

### Phase 1: Nostr Deepening

- Implement NIP-77 for efficient sync
- Add NIP-BE for BLE interoperability
- Cache NIP-05 identities when online

### Phase 2: Cashu Integration

- Add Cashu wallet module
- "Send Cashu" option in chats
- Token display with verification status
- Multiple mint support

### Phase 3: Identity & Credentials

- Expose keypairs as DIDs
- Support importing Verifiable Credentials
- Credential verification UI

### Phase 4: Lightning (Optional)

- NIP-47 wallet connect support
- Watchtower integration
- Channel management UI
- Clear risk warnings

---

## Related Projects

- **[Cashu](https://cashu.space)** - Chaumian e-cash for Bitcoin
- **[Fedimint](https://fedimint.org)** - Federated Bitcoin custody + e-cash
- **[BitChat](https://bitchat.io)** - Cashu over Bluetooth demo
- **[LNMesh](https://decrypt.co/research/ln-mesh)** - Lightning in mesh networks research
- **[DIF](https://identity.foundation)** - Decentralized Identity Foundation

---

**See also:** [Roadmap](../roadmap.md) | [Security](../../security/privacy.md) | [Nostr Transport](../transports/nostr.md)
