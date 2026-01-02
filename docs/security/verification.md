# APK Verification

Verify that you downloaded an authentic Mycel APK.

## Why Verify?

Malicious actors could distribute modified APKs. Verification ensures:

- The APK is from the official source
- It hasn't been tampered with
- It's signed with the official key

## Official Signing Certificate

All official Mycel releases are signed with this certificate:

```
Subject: CN=Dmitry Stakhin, OU=Mycelium Labs, O=Quantum Research Pty Ltd
SHA-256 Fingerprint:
446a854ca21ef946e1ae192a685cde06a791d4ae8e9eb3844a9ef537fa688d6d
```

!!! danger "If the fingerprint doesn't match"
    Do not install the APK. You may have downloaded a tampered version.

## Verification Methods

### Method 1: SHA-256 Checksum

Each release includes a `.sha256` file with the expected checksum.

```bash
# Download both files
wget https://github.com/ethene/mycel-releases/releases/latest/download/Mycel-v*.apk
wget https://github.com/ethene/mycel-releases/releases/latest/download/Mycel-v*.apk.sha256

# Verify checksum
sha256sum -c Mycel-v*.apk.sha256
```

Expected output: `Mycel-vX.X.XXX.apk: OK`

### Method 2: apksigner (Recommended)

Use Android's official verification tool:

```bash
# If you have Android SDK
apksigner verify --print-certs Mycel-v*.apk
```

Verify the SHA-256 fingerprint matches the official certificate above.

### Method 3: keytool

```bash
# Extract and verify certificate
keytool -printcert -jarfile Mycel-v*.apk
```

Check that the SHA-256 fingerprint matches.

### Method 4: On Android

After installation, you can verify using apps like [App Manager](https://github.com/AzizaHelmy/AppManager):

1. Open App Manager
2. Find Mycel
3. Check signature details
4. Compare SHA-256 fingerprint

## Official Download Sources

Only download Mycel from:

| Source | URL |
|--------|-----|
| **GitHub Releases** | [github.com/ethene/mycel-releases](https://github.com/ethene/mycel-releases/releases) |
| **Obtainium** | Via GitHub Releases URL |

!!! warning "Unofficial sources"
    APKs from other sources (APK mirrors, forums, etc.) may be modified. Always verify.

## What If Verification Fails?

1. **Delete the APK** - Do not install it
2. **Re-download from official source** - The file may have been corrupted
3. **Check your network** - Ensure you're not being redirected
4. **Report the issue** - If the official download doesn't verify, report it
