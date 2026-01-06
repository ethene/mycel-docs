# Mesh Broadcast

The **Mesh Broadcast** (shown as "Mesh Group" in the app) is a special built-in channel that connects you with everyone nearby running Mycel.

## What is Mesh Broadcast?

Think of it as a **local walkie-talkie channel** - anyone within range can hear and talk.

| Property | Mesh Broadcast |
|----------|----------------|
| Range | Local mesh only (nearby devices) |
| Who can read | Anyone nearby with Mycel |
| Who can post | Anyone |
| Encryption | None (plaintext) |
| Always on | Yes, cannot unsubscribe |

---

## How It's Different

### vs Direct Messages

| | Direct Message | Mesh Broadcast |
|-|----------------|----------------|
| Recipients | One specific person | Everyone nearby |
| Privacy | End-to-end encrypted | Public/plaintext |
| Delivery | Store-and-forward | Local flood |

### vs Groups

| | Group | Mesh Broadcast |
|-|-------|----------------|
| Membership | Explicit (invite/join) | Automatic (all nearby) |
| Persistence | Long-term | Ephemeral |
| Encryption | Yes | No |
| Scope | Selected members | Geographic |

### vs Channels

| | Channel | Mesh Broadcast |
|-|---------|----------------|
| Author | Single owner | Anyone |
| Subscription | Opt-in | Always on |
| Verification | Signed messages | None |
| Purpose | Announcements | Local chat |

---

## When to Use Mesh Broadcast

### Good Use Cases

- **"Is anyone out there?"** - Finding other Mycel users nearby
- **Local coordination** - Quick messages at events
- **Emergencies** - Reaching anyone within range
- **Testing** - Verifying your mesh connection works
- **Casual chat** - Talking with nearby strangers

### Not Recommended For

- **Private conversations** - Use DMs instead (encrypted)
- **Sensitive information** - Messages are not encrypted
- **Long-term discussions** - Use groups instead
- **Specific recipients** - Use DMs or groups

!!! warning "No encryption"
    Mesh Broadcast messages are **not encrypted**. Anyone on the mesh can read them. Don't share sensitive information.

---

## Using Mesh Broadcast

### Finding It

1. Open Mycel
2. Go to **Chats**
3. Look for **"Mesh Group"** at the top of your conversations
   - Or find it in a dedicated section

### Sending a Message

1. Open Mesh Group
2. Type your message
3. Tap Send

Your message floods across the local mesh immediately.

### Reading Messages

Messages appear in chronological order showing:

- **Sender name** - Who posted
- **Timestamp** - When posted
- **Message text** - The content

### Who Receives Your Messages?

Everyone who:

- Is running Mycel
- Is within Bluetooth/Wi-Fi range (directly or via relays)
- Has the app active (foreground or background)

---

## Privacy Considerations

### What's Visible

When you post to Mesh Broadcast:

- Your **display name** is shown
- Your **device UID** is included
- The **message content** is plaintext
- Your **approximate location** (you're "nearby")

### What's NOT Protected

- Message content is not encrypted
- Anyone on the mesh can read it
- Messages may be relayed through other devices
- No delivery confirmations

### Recommendations

1. **Don't share personal info** - No addresses, phone numbers, etc.
2. **Assume public** - Treat it like shouting in a crowd
3. **Use for coordination only** - Switch to DMs for private follow-ups
4. **Be respectful** - You're talking to strangers

---

## Technical Details

### How It Works

1. You send a message to Mesh Group
2. It's wrapped in a `MeshBroadcastPayload`:
   - Message ID
   - Text content
   - Your UID
   - Your display name
   - Timestamp
3. The message floods across Nearby Connections
4. Every device in range receives and displays it

### Flooding vs Routing

Unlike DMs and groups that use intelligent routing, Mesh Broadcast uses **flooding**:

- Message sent to all connected peers
- Each peer forwards to their peers
- No destination targeting
- Reaches maximum local coverage

### TTL (Time to Live)

Mesh Broadcast messages have a short TTL:

- **4 hours** default
- Prevents old messages from circulating forever
- Appropriate for ephemeral local chat

---

## Notifications

### Controlling Mesh Group Alerts

1. Go to **Settings**
2. Find **Notifications**
3. Toggle **Mesh Group notifications**

When disabled:

- You still receive Mesh Group messages
- They just don't trigger alerts
- Useful if the local mesh is chatty

---

## Use Case Examples

### At a Conference

```
You: "Anyone here from the networking track?"
Stranger: "Yeah! Where's the next session?"
You: "Room 204, starting in 10 min"
```

### During an Emergency

```
You: "Power out in building A, anyone have info?"
Neighbor: "Utility company says 2 hours estimated"
You: "Thanks! Passing that along"
```

### Testing Your Setup

```
You: "Mesh test - can anyone hear me?"
Nearby user: "Got you loud and clear!"
```

### At a Festival

```
You: "Lost my friend near main stage, anyone see someone in a red hat?"
Stranger: "Red hat heading toward food court 5 min ago"
```

---

## Tips

### For Effective Communication

- **Be concise** - Keep messages short
- **Identify yourself** - "This is Alex from tent 5"
- **Be helpful** - Answer questions when you can
- **Stay on topic** - Respect the shared space

### For Privacy

- **Use nicknames** - Don't use your real name if concerned
- **Move to DMs** - For anything private, ask to continue in DM
- **Be vague about location** - Don't share exact addresses

---

## Troubleshooting

### "I don't see Mesh Group"

- It should appear automatically
- Check if you have the latest app version
- Restart the app

### "Nobody responds to my messages"

- **Are others nearby?** Check Mesh Lab > Nearby for neighbors
- **Is your mesh working?** Test with a known contact via DM
- **Be patient** - Others might not be actively watching

### "Too many messages"

- Mute Mesh Group notifications (Settings > Notifications)
- The chat remains available, just quieter

### "I see strange messages"

- Remember, anyone can post
- Ignore spam or inappropriate content
- Report persistent issues through the app

---

## Summary

| Aspect | Mesh Broadcast |
|--------|----------------|
| Purpose | Local mesh chat with anyone nearby |
| Privacy | None - messages are plaintext |
| Best for | Coordination, emergencies, testing |
| Avoid for | Private info, sensitive topics |
| Can disable? | Can mute notifications, but stays subscribed |

---

**Next:** [Groups](groups.md) | [Channels](channels.md) | [Contacts](contacts.md)
