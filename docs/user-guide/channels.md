# Channels

Channels are **read-only broadcasts** where only the owner (author) can post messages. Subscribers receive updates but cannot reply in the channel.

## When to Use Channels

Channels are perfect for:

- **Announcements** - Company updates, community news
- **Newsletters** - Regular updates to followers
- **Alerts** - Emergency notifications, status updates
- **One-way communication** - When you need to broadcast without discussion

!!! tip "Need discussion?"
    If you want members to reply, use a [Group](groups.md) instead.

---

## Channels vs Groups

| Feature | Channel | Group |
|---------|---------|-------|
| Who can post | Owner only | Any member |
| Join method | Subscribe (anyone) | Invite or join |
| Purpose | Broadcasts | Discussions |
| Author verification | Yes (signed) | No |
| Replies | Not in channel | Yes |

---

## Channel Types

### Mesh Local Channels

- **Range:** Local mesh only (nearby devices)
- **Transport:** Nearby Connections (Bluetooth/Wi-Fi)
- **Best for:** Local announcements, event updates

### Global Channels (Future)

- **Range:** Worldwide via internet
- **Transport:** Nostr relays
- **Status:** Planned for future release

---

## Creating a Channel

### Step by Step

1. Open Mycel and go to **Chats**
2. Tap the **Channels** sub-tab
3. Tap the **+** (floating action button)
4. Enter a **Channel Name** (required)
5. Optionally add a **Description**
6. Tap **Create Channel**

You're now the channel owner and can start posting.

### What You Can Do as Owner

- Post messages to all subscribers
- View subscriber count
- Delete the channel
- All your posts are cryptographically signed

---

## Subscribing to Channels

### Finding Channels

Channels can be discovered through:

- **Direct share** - Someone sends you a channel link/ID
- **QR code** - Scan a channel's QR code
- **Nearby discovery** - Channels can broadcast their existence

### Subscribing

1. Open the channel preview
2. Review the channel info:
   - Name and description
   - Owner identity
   - Subscriber count
3. Tap **Subscribe**

After subscribing:

- The channel appears in your Channels tab
- You'll receive new posts as they're broadcast
- You can read all future messages

### Unsubscribing

1. Open the channel
2. Tap the channel header
3. Tap **Unsubscribe**
4. Confirm

You'll stop receiving new messages. The channel moves to your archive.

---

## Reading Channel Messages

### Message Display

Channel messages show:

- **Author name** - Who posted (channel owner)
- **Timestamp** - When posted
- **Message content** - The actual message
- **Verification status** - Signature check passed

### Message Actions

Even though you can't reply in the channel, you can:

- **Copy** - Copy message text
- **Forward** - Send to a contact or group
- **View info** - See message details and verification

---

## Posting to Your Channel

As the channel owner:

1. Open your channel
2. Type your message in the compose area
3. Tap **Send**

Your message is:

1. Signed with your Ed25519 key (proves you wrote it)
2. Encrypted (optional, for private channels)
3. Broadcast to all subscribers via the mesh

### Post Delivery

Channel posts travel through the mesh like other messages:

| Status | Meaning |
|--------|---------|
| Pending | Waiting for relay |
| Forwarding | Traveling through mesh |
| Broadcast | Sent to mesh |

!!! note "No per-subscriber delivery tracking"
    Unlike DMs, channel posts don't track delivery to individual subscribers. Once broadcast, the message spreads through the mesh.

---

## Channel Notifications

### Per-Channel Settings

1. Open the channel
2. Tap the header
3. Toggle **Mute** to silence notifications
4. Choose duration if muting

### Global Settings

In **Settings > Notifications**, you can control:

- Popup notifications for all channels
- Sound and vibration
- (Mesh Group has its own toggle)

---

## Author Verification

### Why It Matters

Anyone could claim to be a channel owner. Mycel prevents impersonation through **cryptographic signatures**.

### How It Works

1. When you create a channel, your public key is associated with it
2. Every post you make is signed with your private key
3. Subscribers verify the signature before displaying the message
4. Invalid signatures are rejected

### What You See

- **Verified** - Signature matches the channel owner
- **Unverified/Warning** - Something doesn't match (rare, investigate)

---

## Managing Your Channel

### Viewing Subscribers

Currently, you can see:

- Total subscriber count
- Channel creation date
- Your ownership status

Individual subscriber lists are not shown (privacy).

### Deleting Your Channel

1. Open your channel
2. Tap the header
3. Tap **Delete Channel**
4. Confirm

!!! warning "Deletion is permanent"
    Deleting a channel removes it from your device. Subscribers may still have cached messages.

---

## Use Cases

### Emergency Alerts

Create a channel for your community or building:

- Post emergency updates
- Share evacuation info
- Coordinate during disasters
- Works without internet

### Event Announcements

At a festival, conference, or gathering:

- Announce schedule changes
- Share location updates
- Post important reminders
- Reaches nearby attendees via mesh

### Community Updates

For neighborhoods or local groups:

- Share community news
- Post meeting reminders
- Announce local events
- No internet required

### Team Broadcasts

For work teams in the field:

- Send instructions to all team members
- Share status updates
- Coordinate activities
- Works in remote areas

---

## Tips

### For Channel Owners

- **Be concise** - Channel messages should be clear and actionable
- **Post sparingly** - Too many posts = subscribers mute you
- **Use descriptive names** - Help people understand your channel's purpose
- **Share your channel** - Give people the ID or QR code to subscribe

### For Subscribers

- **Mute noisy channels** - Keep important channels unmuted
- **Forward useful messages** - Share to contacts who might benefit
- **Unsubscribe from inactive channels** - Keep your list clean

---

## Troubleshooting

### "I'm not receiving channel posts"

- **Check subscription** - Open Channels tab, verify you're subscribed
- **Check mute status** - The channel might be muted
- **Check connectivity** - Go to Mesh Lab > Nearby
- **Is the owner posting?** - The channel might be inactive

### "My posts aren't being delivered"

- **Check connectivity** - Ensure you're connected to the mesh
- **Check Queue** - Go to Mesh Lab > Queue to see pending posts
- **Wait for subscribers** - Posts only reach people in the mesh

### "Signature verification failed"

This is rare and could indicate:

- **Corrupted message** - Try refreshing
- **Impersonation attempt** - Someone trying to fake posts (blocked)
- **Bug** - Report if it persists

---

## Technical Details

For developers and curious users:

| Property | Value |
|----------|-------|
| Encryption | AES-256-GCM (optional) |
| Author signatures | Ed25519 |
| Message TTL | 4 hours default |
| Routing | Topic-based (interest filter) |

**Deep Dive:** [Channels Technical Details](../deep-dive/groups/channels.md)

---

**Next:** [Mesh Broadcast](mesh-broadcast.md) | [Groups](groups.md) | [Contacts](contacts.md)
