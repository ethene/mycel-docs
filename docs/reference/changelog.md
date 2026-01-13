# Changelog

All notable changes to Mycel are documented here.

## [2.1.1] - 2026-01-13

### Fixed

- Version system now reads from version.txt (single source of truth)
- CRDT signature verification for GROUP_UPDATE via Nostr

---

## [2.1.0] - 2026-01-12

**Iran soft launch release**

### Features

- Complete Farsi (fa-IR) localization - 486 strings translated
- Bilingual invite sharing (English + Farsi)
- Nostr-only CRDT propagation for group membership
- Self-managing analytics system with hypothesis dashboard

### Technical

- CRDT over Nostr topic publishing
- Signature verification using raw Ed25519 public keys

---

## [2.0.720] - 2026-01-02

**First public release**

### Features

- End-to-end encrypted direct messages
- Private groups with CRDT membership sync
- Broadcast channels
- Multi-hop mesh routing
- Nearby transport (Google Nearby Connections)
- Nostr transport (internet relay)
- ACK-based delivery confirmation
- PRoPHET + Spray-and-Wait routing
- H3 geographic clustering

### Technical

- Android 8.0+ (API 26+), full mesh features on Android 10+
- Kotlin 1.9.24
- Jetpack Compose UI
- Room database
- R8 minification enabled

---

## Version History

Mycel has been in active development for 97+ sessions. This is the first public release after extensive testing and refinement.

## Release Channels

- **Stable releases**: [github.com/ethene/mycel-releases](https://github.com/ethene/mycel-releases)
- **Automatic updates**: Use [Obtainium](https://github.com/ImranR98/Obtainium)
