# First Steps

After installing Mycel, you'll set up your identity and learn how to send messages.

## Initial Setup

When you first open Mycel:

1. **Grant permissions** - Mycel needs Bluetooth and location permissions to discover nearby devices (see [Permissions](permissions.md) for why)
2. **Your identity is created** - Mycel generates a unique cryptographic identity (public/private key pair)
3. **You're ready to go** - No account registration, no phone number required

!!! info "Your identity is local"
    Your cryptographic keys stay on your device. There's no account to create or server to register with.

## Understanding Your Identity

Mycel identifies you by a **Device ID** - a short code derived from your public key. This is what others use to add you as a contact.

To find your Device ID:

1. Open Mycel
2. Go to **Settings** or tap your profile
3. Your ID will be displayed (you can tap to copy it)

Share this ID with friends so they can message you.

## Adding Contacts

To message someone, you need their Device ID:

1. Tap **+** or **Add Contact**
2. Enter their Device ID
3. Optionally add a nickname
4. Save the contact

Now you can send them messages.

## Sending Your First Message

1. Select a contact from your list
2. Type your message
3. Tap **Send**

Your message will show one of these statuses:

| Status | Meaning |
|--------|---------|
| **Pending** | Waiting for a nearby device to relay |
| **Forwarding** | Handed off, traveling through the mesh |
| **Delivered** | Reached your friend's device |

!!! warning "Messages may take time"
    Unlike instant messengers, Mycel messages travel through physical encounters. Delivery can take minutes, hours, or even days depending on distance and how many Mycel users are around.

## The Mesh Network

Mycel automatically:

- **Advertises** your device to nearby Mycel users
- **Discovers** other Mycel devices in range
- **Exchanges messages** with encountered devices

You don't need to do anything - just keep the app running (it works in the background).

### Tips for Better Connectivity

- **Keep Bluetooth and Wi-Fi on** - Both are used for discovery
- **Allow background activity** - Don't force-stop the app
- **Move around** - More encounters = faster delivery
- **More users = better mesh** - Invite friends to use Mycel

## What's Next?

- [App Updates](updates.md) - Keep Mycel up to date
- [Permissions](permissions.md) - Understand why each permission is needed
- [How Delivery Works](../user-guide/delivery.md) - Learn about message routing
