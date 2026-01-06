# Messaging

This guide covers Direct Messages (DMs) - one-to-one encrypted conversations. For group conversations, see [Groups](groups.md). For broadcast channels, see [Channels](channels.md).

## Direct Messages Overview

Direct messages are:

- **Private** - End-to-end encrypted, only you and recipient can read
- **One-to-one** - Between you and one other person
- **Offline-capable** - Work without internet via mesh
- **Persistent** - Stored until delivered or expired

---

## Sending Messages

### Starting a Conversation

**From Contacts:**

1. Go to **Contacts** tab
2. Tap a contact
3. Tap **Message** to start chatting

**From Chats:**

1. Go to **Chats** tab
2. Tap an existing conversation, or
3. Tap **+** to start a new message
4. Select a contact

### Composing Messages

1. Tap the text input at the bottom
2. Type your message
3. Tap **Send** (arrow icon)

Your message is:

1. Encrypted with the recipient's public key
2. Signed with your private key
3. Queued for delivery via the mesh

### What You Can Send

| Type | Supported | Notes |
|------|-----------|-------|
| Text | Yes | Up to several KB |
| Links | Yes | Tappable URLs |
| Emoji | Yes | Standard emoji |
| Files | Not yet | Planned for future |
| Images | Not yet | Planned for future |
| Voice | Not yet | Planned for future |

---

## Message Status

### Status Icons Explained

| Icon | Status | What It Means | What to Do |
|------|--------|---------------|------------|
| Clock | **Pending** | Message queued, waiting for relay | Wait - a nearby device will relay it |
| Single check | **Forwarding** | Handed off to mesh, traveling | Message is on its way |
| Double check | **Delivered** | Reached recipient's device | Done! They received it |
| Warning/X | **Error** | Something went wrong | Check connectivity, retry |
| "EXP" | **Expired** | TTL exceeded before delivery | Message won't be delivered |

### Status Flow

```
Pending → Forwarding → Delivered
    ↓           ↓
  Error      Expired
```

### Understanding Delivery Times

| Scenario | Typical Time |
|----------|--------------|
| Recipient nearby | Seconds |
| Same building/event | Minutes |
| Same city | Hours |
| Different cities | Hours to days |
| Remote areas | Days or longer |

!!! info "No internet required"
    Messages travel through device-to-device encounters. More Mycel users = faster delivery.

---

## Message Actions

### Long-Press Menu

Long-press any message to see actions:

| Action | Description |
|--------|-------------|
| **Copy** | Copy message text to clipboard |
| **Forward** | Send to another contact/group |
| **Delete** | Remove message |
| **Info** | View message details |

### Copying Messages

1. Long-press the message
2. Tap **Copy**
3. Text is copied to clipboard
4. Paste anywhere

### Forwarding Messages

1. Long-press the message
2. Tap **Forward**
3. Select destination:
   - A contact
   - A group
   - A channel (if you're the owner)
4. Confirm

The forwarded message:

- Shows as sent by you
- Doesn't indicate it's forwarded
- Is re-encrypted for the new recipient

### Deleting Messages

1. Long-press the message
2. Tap **Delete**
3. Confirm

!!! warning "Local delete only"
    Deleting removes the message from your device. The recipient still has their copy.

### Message Info

View details about a message:

1. Long-press the message
2. Tap **Info**
3. See:
   - Message ID
   - Sent timestamp
   - Delivery status
   - Encryption status
   - Recipient info

---

## Conversation Management

### Pinning Conversations

Keep important chats at the top:

1. Go to **Chats** > **Direct Messages**
2. Swipe **right** on a conversation
3. Tap **Pin**

Pinned conversations:

- Stay at the top of your list
- Show a pin indicator
- Remain pinned until you unpin

**To unpin:** Swipe right again and tap **Unpin**

### Archiving Conversations

Hide inactive chats:

1. Swipe **left** on a conversation
2. Tap **Archive**

Archived conversations:

- Move to the **Archived** tab
- Don't appear in main list
- Still receive messages (you'll be notified)

**To unarchive:**

1. Go to **Archived** tab
2. Swipe left on the conversation
3. Tap **Unarchive**

### Organization Tips

| Goal | Action |
|------|--------|
| Prioritize important chats | Pin them |
| Clean up old conversations | Archive them |
| Find a specific chat | Use search |

---

## Searching

### Finding Messages

1. Go to **Chats**
2. Tap the search icon or search bar
3. Type your search query

Search looks in:

- Contact names
- Message content
- Group names

### Search Tips

- Search by name to find a contact quickly
- Search by keyword to find messages about a topic
- Partial matches work (searching "meet" finds "meeting")

---

## Notifications

### Message Notifications

When you receive a message:

- Notification appears (if enabled)
- Badge on app icon updates
- Unread count shown in Chats tab

### Notification Settings

Control notifications in **Settings > Notifications**:

| Setting | Effect |
|---------|--------|
| Popup | Floating preview appears |
| Sound | Audio alert plays |
| Vibration | Phone vibrates |

### Per-Conversation Muting

Mute specific conversations:

1. Open the conversation
2. Tap the header (contact name)
3. Toggle **Mute**
4. Select duration

---

## Encryption

### How It Works

Every DM is end-to-end encrypted:

1. **Key agreement** - Your devices establish a shared secret using X25519
2. **Encryption** - Message encrypted with AES-256-GCM
3. **Signing** - Message signed with Ed25519 to prove it's from you
4. **Transit** - Only encrypted data travels through the mesh
5. **Decryption** - Only the recipient's device can decrypt

### What This Means

- **Relay nodes can't read** - They only see encrypted blobs
- **We can't read** - No server has the keys
- **Only recipient can read** - Encryption is to their specific key

### Verification

Each message is cryptographically verified:

- Signature proves sender identity
- Encryption ensures only recipient reads
- Tampering is detected and rejected

---

## Tips for Effective Messaging

### For Reliability

- **Keep app running** - Background operation is essential
- **Grant permissions** - Location and Bluetooth needed for mesh
- **Be patient** - Mesh delivery takes time
- **Check status** - Watch for delivery confirmation

### For Important Messages

- **Verify delivery** - Wait for double-check before assuming received
- **Use multiple paths** - Nostr + Nearby for faster delivery
- **Consider timing** - Send when recipient is likely active

### For Organization

- **Pin important contacts** - Quick access to frequent chats
- **Archive old conversations** - Keep list clean
- **Use search** - Find old messages quickly

---

## Troubleshooting

### "Message stuck on Pending"

- **Check connectivity** - Go to Mesh Lab > Nearby
- **Are others nearby?** - Need relay devices
- **Wait** - Messages queue until opportunity arises
- **Check Queue** - Mesh Lab > Queue shows status

### "Message shows Error"

- **Check recipient** - Is their UID correct?
- **Retry** - Go to Queue, tap message, retry
- **Check connectivity** - Ensure mesh is active

### "Not receiving messages"

- **Check notifications** - Are they enabled?
- **Check mute** - Is the conversation muted?
- **Check background** - Is Mycel running?
- **Check battery** - Is the app being killed?

### "Messages taking too long"

- **Normal for mesh** - Delivery time varies
- **Check Nostr** - Internet relay speeds things up
- **More users help** - Encourage friends to use Mycel

---

## Conversation Types Comparison

| Feature | Direct Message | Group | Channel |
|---------|----------------|-------|---------|
| Participants | 2 | Multiple | Unlimited |
| Who can post | Both | Any member | Owner only |
| Encryption | E2E | Shared key | Optional |
| Best for | Private chat | Team discussion | Announcements |
| Guide | This page | [Groups](groups.md) | [Channels](channels.md) |

---

**Next:** [Groups](groups.md) | [Channels](channels.md) | [How Delivery Works](delivery.md)
