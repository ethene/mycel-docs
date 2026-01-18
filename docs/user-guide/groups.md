# Groups

Groups let multiple people chat together. Unlike channels, **any member can post messages**.

<div class="screenshot-center">
  <div class="phone-mockup">
    <img src="../../assets/screenshots/en/groups-list.png" alt="Groups list">
  </div>
</div>

## Groups vs Other Conversation Types

| Feature | Direct Message | Group | Channel |
|---------|----------------|-------|---------|
| Participants | 2 people | Multiple members | Unlimited subscribers |
| Who can post | Both | Any member | Owner only |
| Join method | Add contact | Invite (private) or join (public) | Subscribe |
| Encryption | Yes | Yes | Yes |
| Best for | Private 1:1 | Team chats, friends | Announcements |

## Types of Groups

### Private Groups

- **Invite-only** - Members must be invited by an admin
- **Hidden** - Don't appear in public discovery
- **Secure** - Group key shared only via encrypted invites

### Public Groups

- **Open** - Anyone can join by knowing the group ID
- **Discoverable** - Can appear in group announcements
- **Convenient** - Easier to share, less secure

---

## Creating a Group

### Step by Step

1. Open Mycel and go to the **Chats** tab
2. Tap the **Groups** sub-tab
3. Tap the **+** (floating action button)
4. Enter a **Group Name** (required)
5. Optionally add a **Description**
6. Choose **Private** or **Public**:
   - Private: Members must be invited
   - Public: Anyone can join with the group ID
7. Tap **Create Group**

You're now the group's admin and can start inviting members.

### What Happens Behind the Scenes

When you create a group:

1. A unique group ID is generated
2. A 256-bit encryption key is created for the group
3. You become the admin (only admins can invite/remove members in private groups)
4. The group is ready to use immediately

---

## Inviting Members

### To a Private Group

As the admin:

1. Open the group chat
2. Tap the group name/header to view group info
3. Tap **Invite Members** or **Add Member**
4. Select contacts from your contact list
5. Tap **Send Invite**

The invite is encrypted specifically for each recipient. When they accept:

- They receive the group key
- They're added to the member list
- They can see group messages and post

### To a Public Group

For public groups, you can:

- Share the group ID directly (they can join themselves)
- Use the invite flow above (works the same way)

!!! tip "Invites work offline"
    Group invites travel through the mesh like regular messages. Recipients can accept invites even if you're not online at the same time.

---

## Receiving and Accepting Invites

When someone invites you to a group:

1. You'll receive a notification (if enabled)
2. The invite appears in your message list
3. Tap the invite to view group details:
   - Group name
   - Who invited you
   - Current member count
4. Tap **Join Group** to accept

After joining:

- The group appears in your Groups tab
- You can see message history (from the time of your join)
- You can post messages

### Declining Invites

Simply ignore the invite or dismiss it. You won't be added to the group.

---

## Managing Group Membership

### Viewing Members

1. Open the group chat
2. Tap the group header
3. View the member list with roles:
   - **Admin** - Can invite/remove members
   - **Member** - Can read and post

### Adding Members (Admin Only)

1. Open group info
2. Tap **Add Member** or **Invite**
3. Select contacts
4. Send invites

### Removing Members (Admin Only)

1. Open group info
2. Find the member in the list
3. Tap their name or the remove option
4. Confirm removal

!!! warning "Removed members keep old messages"
    When you remove someone, they lose access to new messages but can still read messages they received before removal. The group key is not rotated.

### Promoting to Admin

Admins can promote other members:

1. Open group info
2. Tap a member's name
3. Select **Make Admin**

---

## Posting in Groups

Posting works like direct messages:

1. Open the group
2. Type your message
3. Tap **Send**

Your message is:

1. Encrypted with the group key
2. Sent to all members via the mesh
3. Delivered as members come into contact with relays

### Message Delivery in Groups

Group messages travel the same way as direct messages - through the mesh. Each member receives the message independently, so delivery times may vary per member.

| Status | Meaning |
|--------|---------|
| Pending | Waiting for relay opportunity |
| Forwarding | Traveling through mesh |
| Delivered | Reached at least one member |

---

## Leaving a Group

To leave a group:

1. Open the group chat
2. Tap the group header
3. Scroll to the bottom
4. Tap **Leave Group**
5. Confirm

After leaving:

- You're removed from the member list
- You won't receive new messages
- The group remains in your history (archived)
- You can't rejoin private groups without a new invite

---

## Group Notifications

### Muting a Group

To mute notifications for a specific group:

1. Open the group
2. Tap the group header
3. Toggle **Mute** on
4. Choose mute duration (1 hour, 8 hours, 1 day, forever)

Muted groups still receive messages, but won't trigger notifications.

### Global Group Settings

In **Settings > Notifications**:

- **Mesh Group notifications** - Toggle alerts for the built-in mesh broadcast
- **Popup notifications** - Show floating alerts
- **Sound** / **Vibration** - Audio and haptic feedback

---

## Group Sync and CRDT

Groups use a special technology called **CRDT** (Conflict-free Replicated Data Type) to sync membership across all members, even when they're offline.

### How It Works

- Each member change (add/remove) is recorded with a timestamp
- When members reconnect, changes are merged automatically
- The same final state is reached regardless of the order changes are received

### What This Means for You

- **Offline-friendly** - Membership changes sync whenever devices meet
- **Consistent** - Everyone eventually sees the same member list
- **No conflicts** - Simultaneous changes are handled automatically

---

## Tips for Groups

### For Better Delivery

- **Keep the app running** - Background operation helps relay group messages
- **More members = better mesh** - Each member can relay to others
- **Be patient** - Group messages may take longer than DMs (more recipients)

### For Privacy

- **Use private groups** for sensitive discussions
- **Verify invites** - Make sure you know who invited you
- **Admin carefully** - Only promote trusted members to admin

### For Organization

- **Use descriptive names** - Makes groups easy to find
- **Archive old groups** - Swipe left to archive inactive groups
- **Pin important groups** - Swipe right to pin frequently used groups

---

## Troubleshooting

### "I can't add someone to my group"

- **Are you an admin?** Only admins can invite members in private groups
- **Do you have their contact?** You can only invite people in your contact list
- **Is the invite stuck?** Check Mesh Lab > Queue for pending invites

### "I didn't receive a group invite"

- **Check your message list** - Invites appear as special messages
- **Is the sender nearby?** Invites travel through the mesh like messages
- **Check permissions** - Ensure Mycel has notification permission

### "Group messages aren't arriving"

- **Check connectivity** - Go to Mesh Lab > Nearby to see if you're connected
- **Is the group muted?** Check group settings
- **Are other members online?** Group messages need relay paths

### "I accidentally left a group"

- **Private groups** - Ask an admin to send a new invite
- **Public groups** - Rejoin using the group ID

---

## Technical Details

For developers and curious users:

| Property | Value |
|----------|-------|
| Encryption | AES-256-GCM with shared group key |
| Key distribution | ECIES (X25519 + XChaCha20-Poly1305) |
| Membership sync | 2P-Set CRDT |
| Max members | ~100 (practical limit) |
| Message TTL | 24 hours default |

**Deep Dive:** [Private Groups Technical Details](../deep-dive/groups/private-groups.md)

---

**Next:** [Channels](channels.md) | [Mesh Broadcast](mesh-broadcast.md) | [Contacts](contacts.md)
