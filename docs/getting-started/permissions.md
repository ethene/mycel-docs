# Permissions

Mycel requires several permissions to discover nearby devices and maintain connections. Here's why each permission is needed.

## Required Permissions

### Location (Fine & Background)

**Why:** Android requires location permission to scan for Bluetooth and Wi-Fi devices. This is an Android system requirement, not a Mycel design choice.

**What Mycel does:** Scans for nearby Mycel devices using Bluetooth Low Energy.

**What Mycel doesn't do:** Track your GPS location, store location history, or share your location with anyone.

!!! info "Background location"
    For Mycel to relay messages when you're not actively using the app, it needs background location permission. Without this, the mesh network only works when the app is open.

### Bluetooth

**Why:** Mycel uses Bluetooth Low Energy (BLE) to discover and connect with nearby devices.

**What happens:** Your phone advertises itself to other Mycel users and scans for them.

### Nearby Wi-Fi Devices (Android 13+)

**Why:** On Android 13 and later, this permission is required for Wi-Fi Direct connections, which provide faster data transfer than Bluetooth.

### Notifications

**Why:** To alert you when messages arrive, especially when the app is in the background.

### Battery Optimization Exemption

**Why:** Android may kill background apps to save battery. Mycel needs to run continuously to relay messages.

**How to grant:** When prompted, tap "Allow" to exempt Mycel from battery optimization. You can also do this manually in Settings > Apps > Mycel > Battery > Unrestricted.

## Permission Summary

| Permission | Purpose | Can Deny? |
|------------|---------|-----------|
| Fine Location | BLE scanning | App won't work |
| Background Location | Background mesh operation | Limited functionality |
| Bluetooth | Device discovery | App won't work |
| Nearby Wi-Fi | Faster connections | Reduced speed |
| Notifications | Message alerts | Miss notifications |
| Battery | Background operation | Unreliable delivery |

## Privacy Assurance

Mycel:

- Does **not** upload your location anywhere
- Does **not** track your movements
- Does **not** share any data with servers (there are no servers)
- Uses location permission **only** for Bluetooth scanning

Your location data stays on your device. The permission is purely a technical requirement imposed by Android's Bluetooth APIs.

## Troubleshooting

### "Mycel can't find nearby devices"

Check that:

1. Location permission is granted
2. Location services are enabled (phone settings)
3. Bluetooth is turned on
4. Wi-Fi is turned on (even if not connected to a network)

### "Messages not sending in background"

Check that:

1. Background location is granted
2. Battery optimization is disabled for Mycel
3. Your phone's battery saver isn't killing the app

### Revoking Permissions

You can revoke permissions anytime in Settings > Apps > Mycel > Permissions. However, Mycel won't function properly without the core permissions (Location, Bluetooth).
