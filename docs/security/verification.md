# APK Verification

Verify that you downloaded an authentic Mycel APK.

## Quick Start: Use Obtainium

The easiest way to install and verify Mycel is with [Obtainium](https://github.com/ImranR98/Obtainium):

1. Install Obtainium from [F-Droid](https://f-droid.org/packages/dev.imranr.obtainium.fdroid/) or [GitHub](https://github.com/ImranR98/Obtainium/releases)
2. Add Mycel: `https://github.com/ethene/mycel-releases`
3. Install - Obtainium verifies signatures automatically

For manual verification, continue below.

---

## Why Verify?

Malicious actors could distribute modified APKs. Verification ensures:

- The APK is from the official source
- It hasn't been tampered with
- It's signed with the official key

## Official Signing Certificate

All official Mycel releases are signed with this certificate:

| Field | Value |
|-------|-------|
| **Subject** | CN=Dmitry Stakhin, OU=Mycelium Labs, O=Quantum Research Pty Ltd, L=Brisbane, ST=QLD, C=AU |
| **SHA-256** | `446a854ca21ef946e1ae192a685cde06a791d4ae8e9eb3844a9ef537fa688d6d` |
| SHA-1 | `592aba950d84078284afaa14f6b533fe7d9b8306` |

!!! danger "If the fingerprint doesn't match"
    Do not install the APK. You may have downloaded a tampered version.

---

## Method 1: SHA-256 Checksum

Each release includes a `.sha256` file with the expected checksum.

### Linux

```bash
sha256sum Mycel-v*.apk
cat Mycel-v*.apk.sha256
# Compare the two outputs
```

### macOS

```bash
shasum -a 256 Mycel-v*.apk
cat Mycel-v*.apk.sha256
```

### Windows (PowerShell)

```powershell
Get-FileHash -Algorithm SHA256 Mycel-v*.apk
Get-Content Mycel-v*.apk.sha256
```

### Windows (CMD)

```cmd
certutil -hashfile Mycel-v2.0.723.apk SHA256
type Mycel-v2.0.723.apk.sha256
```

!!! success "Match = OK"
    If checksums match, the file hasn't been modified.

---

## Method 2: Certificate Verification

### Using apksigner (Android SDK)

```bash
apksigner verify --print-certs Mycel-v*.apk
```

Look for:
```
Signer #1 certificate SHA-256 digest: 446a854ca21ef946e1ae192a685cde06a791d4ae8e9eb3844a9ef537fa688d6d
```

### Using keytool (Java)

```bash
keytool -printcert -jarfile Mycel-v*.apk
```

Check the SHA-256 fingerprint matches the official certificate.

---

## Official Download Sources

| Source | URL | Verification |
|--------|-----|--------------|
| **GitHub Releases** | [github.com/ethene/mycel-releases](https://github.com/ethene/mycel-releases/releases) | Manual |
| **Obtainium** | `https://github.com/ethene/mycel-releases` | Automatic |

!!! warning "Unofficial sources"
    APKs from other sources (APK mirrors, forums, Telegram) may be modified. Always verify or use Obtainium.

---

## What If Verification Fails?

1. **Delete the APK** - Do not install it
2. **Re-download from official source** - File may have been corrupted
3. **Try a different browser** - Some browsers modify downloads
4. **Report the issue** - [Open an issue](https://github.com/ethene/mycel-releases/issues) if official downloads don't verify
