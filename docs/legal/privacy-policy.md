# Privacy Policy

**Last updated:** January 2, 2026

## Overview

Mycel is a decentralized, peer-to-peer messaging application. This privacy policy explains what data Mycel collects, how it's used, and your rights.

**The short version:** Mycel collects no personal data. Messages are end-to-end encrypted. There are no servers storing your information.

## Data We Collect

### Data We DON'T Collect

- Personal information (name, email, phone number)
- Location data
- Usage analytics
- Message content
- Contact lists
- Device identifiers sent to any server

### Data Stored on Your Device

Mycel stores the following **locally on your device only**:

| Data | Purpose |
|------|---------|
| Cryptographic keys | Your identity (never leaves device) |
| Messages | Your conversations (encrypted) |
| Contacts | People you communicate with |
| Routing tables | For message delivery optimization |

This data is stored in a local database and is not transmitted to any server.

## How Messages Work

### Peer-to-Peer Relay

Messages travel through other Mycel users' devices to reach their destination. When relaying:

- Other devices temporarily store your encrypted message
- They cannot decrypt or read the content
- They can see destination identifiers (to route the message)
- Messages are deleted after delivery or expiration

### End-to-End Encryption

All messages are encrypted on your device before transmission. Only the intended recipient can decrypt them. Not even relay devices can read your messages.

## Third-Party Services

### Nostr (Optional)

If you enable Nostr transport:

- Messages are relayed through public Nostr servers
- These servers may log connection metadata
- Message content remains encrypted
- You can disable Nostr for maximum privacy

### Google Nearby Connections

Mycel uses Google's Nearby Connections API for local device discovery. This API operates locally and does not send data to Google servers.

## Your Rights

You can:

- **Delete your data** by uninstalling the app
- **Disable Nostr** for offline-only operation
- **View stored data** in the app's data directory
- **Export your messages** (feature planned)

## Data Retention

- Messages are stored until delivered or expired (TTL)
- Local data persists until you uninstall the app
- No data is retained on any server (there are no servers)

## Security

We implement:

- End-to-end encryption (X25519 + AES-GCM)
- Digital signatures (Ed25519)
- No central points of failure
- Open source code for transparency

## Children's Privacy

Mycel is not directed at children under 13. We do not knowingly collect information from children.

## Changes to This Policy

We may update this policy. Changes will be noted in the changelog and on this page.

## Contact

For privacy concerns, open an issue on GitHub or contact the maintainers.

---

*Mycel is open source software. This policy reflects our commitment to user privacy.*
