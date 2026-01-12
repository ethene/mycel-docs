# Frequently Asked Questions

## General

### What is Mycel?

Mycel is a delay-tolerant mesh messenger for Android. It sends messages by hopping between nearby phones, without requiring internet connectivity.

### Why "Mycel"?

Named after mycelium - the underground fungal network that connects trees in a forest. Like mycelium, Mycel connects devices in a resilient, decentralized network.

### Is Mycel free?

Yes, Mycel is free and open source.

### Can I use Mycel for emergencies?

**No.** Mycel is not suitable for emergency communications. Delivery can take hours or days with no guarantee of arrival. For emergencies, use official emergency services.

### Is Mycel on the Play Store?

Not currently. Download from [GitHub Releases](https://github.com/ethene/mycel-releases/releases).

---

## Connectivity

### Why didn't my message send immediately?

Mycel needs to find a nearby device to relay your message. If no Mycel users are around, the message waits until an encounter happens.

### Can I use Mycel without internet?

Yes! That's the primary use case. Mycel uses Bluetooth and Wi-Fi Direct to communicate directly with nearby devices.

### How far can messages travel?

With enough relay devices, messages can travel anywhere. Each hop typically covers ~100 meters. Messages ride along with people as they move.

### Why does delivery take so long?

Messages travel through physical encounters between devices. More distance = more hops = more time. This is the trade-off for not requiring internet.

---

## Permissions

### Why does Mycel need location permission?

Android requires location permission to scan for Bluetooth devices. Mycel doesn't actually track or store your location - it only uses this permission for device discovery.

### Why does Mycel need background permission?

To relay messages when you're not actively using the app. Without background permission, the mesh only works when the app is open.

---

## Privacy & Security

### Are my messages encrypted?

Yes, all messages are end-to-end encrypted. Only you and your recipient can read them.

### Can relay devices read my messages?

No. Messages are encrypted before leaving your device. Relay devices can only see encrypted data and routing metadata.

### Is there a server storing my messages?

No. Messages are stored only on devices - yours and the relay devices carrying them. No central server exists.

### Do I need a phone number?

No. Mycel identifies you by a cryptographic key, not phone number or email.

---

## Technical

### What Android version do I need?

Android 10 or higher (API 29+). Google Play Services is also required.

### How much storage does Mycel use?

The app is ~50 MB. Message storage varies with usage.

### Does Mycel drain my battery?

Mycel uses Bluetooth scanning which does consume some battery. Battery optimization settings can help balance connectivity vs. battery life.

### What is the Nostr transport?

Nostr is an optional internet-based relay. When available, it can speed up delivery. Messages are still encrypted.

---

## Troubleshooting

### Messages stuck on "Pending"

- Check that Bluetooth and Wi-Fi are enabled
- Ensure location permission is granted
- Make sure the app isn't being killed by battery optimization
- Be patient - mesh delivery takes time

### App keeps closing

- Disable battery optimization for Mycel
- Grant all requested permissions
- Check that your device meets the requirements

### Can't find nearby devices

- Both devices need Mycel running
- Check Bluetooth and Wi-Fi are on
- Check location services are enabled
- Try moving closer together

---

## Updates

### How do I update Mycel?

Download the latest APK from [GitHub Releases](https://github.com/ethene/mycel-releases/releases) and install over your existing version.

### Can I get automatic updates?

Yes, use [Obtainium](https://github.com/ImranR98/Obtainium) to monitor for new releases.
