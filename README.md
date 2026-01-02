# Mycel Documentation

[![Documentation](https://img.shields.io/badge/docs-ethene.github.io%2Fmycel--docs-blue)](https://ethene.github.io/mycel-docs)
[![Get it on Obtainium](https://img.shields.io/badge/Obtainium-Get%20Mycel-green)](https://apps.obtainium.imranr.dev/redirect.html?r=obtainium://add/https://github.com/ethene/mycel-releases)

Official documentation for **Mycel** - a delay-tolerant mesh messenger for Android.

## What is Mycel?

Mycel is a messenger that works **without internet**. Messages hop between nearby phones using Bluetooth and Wi-Fi Direct, eventually reaching their destination even when you're offline.

- **No internet required** - Works completely offline via mesh networking
- **No phone number** - Identified by cryptographic key, not personal info
- **End-to-end encrypted** - Only you and the recipient can read messages
- **No central server** - Messages travel peer-to-peer through the mesh

## Quick Start

### Install Mycel

**Recommended: Use Obtainium for automatic updates**

[![Get it on Obtainium](https://raw.githubusercontent.com/ImranR98/Obtainium/main/assets/graphics/badge_obtainium.png)](https://apps.obtainium.imranr.dev/redirect.html?r=obtainium://add/https://github.com/ethene/mycel-releases)

1. Install [Obtainium](https://github.com/ImranR98/Obtainium) from F-Droid or GitHub
2. Click the badge above, or manually add: `https://github.com/ethene/mycel-releases`
3. Install Mycel - Obtainium handles updates automatically

**Manual install:** Download APK from [Releases](https://github.com/ethene/mycel-releases/releases)

### Read the Docs

**[ethene.github.io/mycel-docs](https://ethene.github.io/mycel-docs)**

- [Getting Started](https://ethene.github.io/mycel-docs/getting-started/install/)
- [How It Works](https://ethene.github.io/mycel-docs/how-it-works/overview/)
- [FAQ](https://ethene.github.io/mycel-docs/reference/faq/)
- [Privacy Policy](https://ethene.github.io/mycel-docs/legal/privacy-policy/)

## Repository Structure

```
docs/
├── index.md              # Landing page
├── getting-started/      # Installation and first steps
├── user-guide/           # Daily usage
├── how-it-works/         # Technical overview
├── deep-dive/            # Academic/research details
├── security/             # Privacy and verification
├── reference/            # FAQ, changelog
└── legal/                # Privacy policy, terms
```

## Building Locally

```bash
# Install dependencies
pip install mkdocs-material

# Preview site
mkdocs serve

# Build for production
mkdocs build
```

## Contributing

Documentation improvements are welcome! Please open a PR or issue.

## Related Repositories

| Repository | Purpose |
|------------|---------|
| [mycel-releases](https://github.com/ethene/mycel-releases) | APK downloads |
| mycel (private) | Source code |

## License

Documentation is licensed under CC BY-SA 4.0.
