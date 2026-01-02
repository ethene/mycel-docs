# Messaging

Mycel supports direct messages (DMs), private groups, and channels.

## Direct Messages (DMs)

One-to-one encrypted conversations.

### Sending a DM

1. Select a contact or tap **New Message**
2. Type your message
3. Tap **Send**

Messages are end-to-end encrypted - only you and your recipient can read them.

### Message Status

| Icon | Status | Meaning |
|------|--------|---------|
| Clock | Pending | Waiting for relay opportunity |
| Single check | Forwarding | Handed off, traveling through mesh |
| Double check | Delivered | Reached recipient's device |

## Private Groups

Group chats with multiple members. All messages are encrypted.

### Creating a Group

1. Tap **New Group**
2. Add members by their Device ID
3. Name your group
4. Start chatting

### Group Features

- **Encrypted** - Messages protected with a shared group key
- **Offline** - Works without internet, like DMs
- **Membership sync** - Group membership syncs when devices meet

## Channels

Broadcast channels for one-to-many communication.

### How Channels Work

- **Owner** can post messages
- **Subscribers** receive broadcasts
- **Read-only** for subscribers

Useful for announcements, news, or updates to a group of followers.

## Message Types

Mycel currently supports:

- **Text messages** - Plain text up to several KB
- **Acknowledgments** - Delivery confirmations

!!! note "Attachments"
    File and image attachments are planned for a future release.

## Tips

### For Faster Delivery

- Keep the app running in background
- Stay in areas with other Mycel users
- Enable both Bluetooth and Wi-Fi

### For Reliability

- Important messages may be worth sending via multiple contacts
- Be patient - delivery can take hours in sparse networks
- Check delivery status to confirm receipt
