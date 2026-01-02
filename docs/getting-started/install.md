# Installation

Mycel is distributed as an APK file that you install directly on your Android device.

## Requirements

- **Android 8.0 (Oreo) or higher** (API 26+)
- **Bluetooth and Wi-Fi** enabled
- Approximately **50 MB** of storage

## Download

Get the latest release from GitHub:

**[Download Mycel APK](https://github.com/ethene/mycel-releases/releases/latest){ .md-button .md-button--primary }**

Each release includes:

- `Mycel-vX.X.XXX.apk` - The application
- `Mycel-vX.X.XXX.apk.sha256` - Checksum for verification

## Verify the Download (Optional)

To ensure you downloaded an authentic, unmodified APK:

```bash
# Check SHA-256 checksum
sha256sum Mycel-*.apk
# Compare with the .sha256 file contents
```

See [APK Verification](../security/verification.md) for detailed instructions and the official signing certificate fingerprint.

## Install on Android

### Step 1: Enable Unknown Sources

Since Mycel isn't on the Play Store, you need to allow installation from unknown sources:

1. Open **Settings** > **Security** (or **Privacy**)
2. Find **Install unknown apps** or **Unknown sources**
3. Enable for your browser or file manager

!!! note "This is a one-time setup"
    You only need to do this once. After installation, you can disable it again if you prefer.

### Step 2: Install the APK

1. Open the downloaded `.apk` file
2. Tap **Install**
3. Wait for installation to complete
4. Tap **Open** to launch Mycel

## Automatic Updates with Obtainium

For automatic update notifications, we recommend [Obtainium](https://github.com/ImranR98/Obtainium):

1. Install Obtainium from [F-Droid](https://f-droid.org/packages/dev.imranr.obtainium.fdroid/) or [GitHub](https://github.com/ImranR98/Obtainium/releases)
2. Add app with URL: `https://github.com/ethene/mycel-releases`
3. Obtainium will notify you when updates are available

## Troubleshooting

### "App not installed" error

- Make sure you have enough storage space
- Try uninstalling any previous version first
- Ensure the APK downloaded completely (check file size)

### "Parse error" or "Package appears to be corrupt"

- Re-download the APK
- Verify the checksum matches

### Installation blocked by Play Protect

1. Tap **More details**
2. Tap **Install anyway**

Google Play Protect may warn about apps from outside the Play Store. This is normal for sideloaded apps.

---

**Next:** [First Steps](first-steps.md) - Set up your identity and send your first message
