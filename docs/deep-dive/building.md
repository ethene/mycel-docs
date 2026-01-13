# Building from Source

This guide covers building Mycel from source code for developers.

---

## Requirements

### System Requirements

| Requirement | Version |
|-------------|---------|
| **JDK** | 17 (required) |
| **Android SDK** | API 36 (compile) |
| **Gradle** | 8.x (wrapper included) |

### Recommended Setup

- **IDE**: Android Studio Ladybug or newer
- **OS**: macOS, Linux, or Windows with WSL

---

## Build Configuration

### SDK Versions

| Configuration | Value |
|---------------|-------|
| `compileSdk` | 36 |
| `minSdk` | 26 (Android 8.0) |
| `targetSdk` | 34 (Android 14) |

### Key Dependencies

| Dependency | Version |
|------------|---------|
| Kotlin | 2.1.0 |
| Android Gradle Plugin | 8.10.0 |
| Hilt | 2.57.2 |
| Room | 2.8.3 |
| Compose BOM | 2024.06.00 |

---

## Getting the Source

```bash
git clone https://github.com/ethene/mycel.git
cd mycel
```

---

## Building

### Set Java 17

Ensure JDK 17 is available:

```bash
# macOS with Homebrew
brew install openjdk@17
export JAVA_HOME=$(/usr/libexec/java_home -v 17)

# Verify
java -version
# Should show: openjdk version "17.x.x"
```

### Debug Build

```bash
./gradlew assembleDebug
```

The APK will be at: `app/build/outputs/apk/debug/app-debug.apk`

### Release Build

Release builds require signing configuration. Create `app/signing.properties`:

```properties
storeFile=path/to/keystore.jks
storePassword=your_password
keyAlias=your_alias
keyPassword=your_key_password
```

Then build:

```bash
./gradlew assembleRelease
```

---

## Installing

### Using ADB

```bash
adb install app/build/outputs/apk/debug/app-debug.apk
```

### Using Build Script

The project includes a build-and-install script:

```bash
./tools/build/rebuild_install_run_autodetect.sh
```

This script:
1. Detects connected devices
2. Builds the debug APK
3. Installs on all devices
4. Starts the app
5. Sets up port forwarding for debugging

---

## Module Structure

```
mycel/
├── app/                    # Main application module
├── core/
│   ├── dtn/               # DTN protocol, bundles
│   ├── nearby/            # Nearby Connections transport
│   ├── ble/               # BLE transport
│   ├── transport/         # Transport abstraction
│   └── capabilities/      # Device feature detection
├── feature/
│   └── ui/                # Jetpack Compose UI
└── tools/                 # Development utilities
```

---

## Testing

### Unit Tests

```bash
# All tests
./gradlew test

# Specific module
./gradlew :core:nearby:test
./gradlew :core:dtn:test
```

### Instrumented Tests

```bash
./gradlew connectedAndroidTest
```

---

## Development Tools

### Developer Bridge

The app includes a JSON-RPC bridge for debugging. After installation:

```bash
# Set up port forwarding
adb forward tcp:8875 tcp:8765

# Check status
curl -X POST -H 'Content-Type: application/json' \
  -d '{"method":"mesh.service.status"}' \
  http://localhost:8875/
```

### meshctl CLI

The `meshctl` tool provides command-line access to the bridge:

```bash
./tools/bridge/meshctl.py --serial DEVICE_SERIAL status
./tools/bridge/meshctl.py --serial DEVICE_SERIAL nearby diag
```

---

## Common Issues

### "JDK 17 required"

Set `JAVA_HOME` to a JDK 17 installation:

```bash
export JAVA_HOME=/path/to/jdk17
```

### "SDK not found"

Ensure `ANDROID_HOME` or `ANDROID_SDK_ROOT` is set:

```bash
export ANDROID_HOME=~/Android/Sdk
```

### Gradle Daemon Issues

Clear the Gradle cache if builds fail unexpectedly:

```bash
./gradlew --stop
rm -rf ~/.gradle/caches/
```

---

## Version Management

The app version is controlled by a single file:

```
app/src/main/assets/version.txt
```

The build system reads this file and generates:
- `versionName` for display
- `versionCode` for Play Store (if ever published)

---

## Release Process

Releases are managed via GitHub:

1. Update `version.txt`
2. Run `./tools/release/prepare_release.sh`
3. CI builds and publishes the APK to GitHub Releases

---

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make changes
4. Run tests: `./gradlew test`
5. Submit a pull request

---

**See also:** [Architecture Overview](architecture/overview.md) | [Message Flow](architecture/data-flow.md)
