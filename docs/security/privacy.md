# Privacy

Mycel is designed with privacy as a core principle.

## What's Protected

### Message Content

- **End-to-end encrypted** using X25519 key agreement + HKDF-SHA256 + AES-256-GCM
- Only sender and recipient can decrypt
- Relay devices see only encrypted blobs

### Your Identity

- Identified by **cryptographic public key**, not phone number
- No account registration required
- No personal information collected

### Communication Patterns

- **No central server** logging who messages whom
- No company has access to your metadata
- Messages travel peer-to-peer through the mesh

## What's Visible to Relay Devices

When another Mycel device relays your message, it can see:

| Data | Visible? | Notes |
|------|----------|-------|
| Message content | No | Encrypted |
| Your real name | No | Not transmitted |
| Sender public key | Yes | Part of routing |
| Recipient public key | Yes | For delivery |
| Message size | Yes | Approximate |
| Timestamp | Yes | Approximate |

This is similar to a sealed letter - the carrier knows the destination but can't read the contents.

## What's Visible on Nostr (Internet Relay)

When using the optional Nostr transport:

- Nostr relay operators can see the same metadata as any relay device
- They can see when your device connects
- They **cannot** read message content (still encrypted)

!!! note "Nostr is optional"
    If maximum privacy is required, you can disable Nostr and use only local mesh delivery.

## Comparison with Other Apps

| Feature | Mycel | WhatsApp | Signal |
|---------|-------|----------|--------|
| E2E Encryption | Yes | Yes | Yes |
| Central server | No | Yes | Yes |
| Phone number required | No | Yes | Yes |
| Metadata logged | No | Yes | Limited |
| Works offline | Yes | No | No |

## Threat Model

Mycel protects against:

- Mass surveillance (no central point to tap)
- Server compromise (no servers)
- Network analysis (distributed, encrypted)
- Eavesdropping (end-to-end encryption)

Mycel does **not** protect against:

- Device compromise (if your phone is hacked)
- Targeted attacks by sophisticated adversaries
- Traffic analysis by a party monitoring all relay devices

## Best Practices

1. **Keep your device secure** - Use strong PIN/biometric
2. **Keep Mycel updated** - Security fixes in newer versions
3. **Verify contacts out-of-band** - Compare Device IDs in person
4. **Consider Nostr trade-offs** - More metadata exposure for faster delivery
