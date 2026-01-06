# Contacts

Contacts are people you can message directly. This guide covers understanding your identity, sharing it with others, and managing your contact list.

## Your Identity

### What Identifies You

In Mycel, you're identified by a **cryptographic key pair**, not a phone number or email:

| Component | Description | Shareable? |
|-----------|-------------|------------|
| **Public Key** | Your identity that others see | Yes |
| **Private Key** | Secret key for signing/decrypting | Never |
| **Device UID** | Short ID derived from public key | Yes |
| **MycTag** | Human-readable fingerprint | Yes |

### Finding Your Identity

1. Open Mycel
2. Go to **Settings**
3. Tap **Profile** or your avatar
4. View your:
   - QR code (for easy sharing)
   - Device UID (short and full hex)
   - Public key fingerprint
   - Display name

### Understanding UIDs

Your **Device UID** is a unique identifier:

- **Short form:** 10-12 characters, easier to share verbally
- **Full hex:** Complete identifier for technical use

Example: `LH6nQx...` (short) or `4c48366e51782e...` (full)

---

## Sharing Your Identity

### Via QR Code

The easiest way to share:

1. Go to **Settings > Profile**
2. Your QR code is displayed
3. Have the other person scan it with Mycel

The QR code contains:

- Your public key
- Your display name
- Optional: location hint

### Via Copy/Paste

1. Go to **Settings > Profile**
2. Tap **Copy** next to your UID
3. Send via any messaging app
4. Recipient enters the UID manually in Mycel

### Via System Share

1. Go to **Settings > Profile**
2. Tap **Share**
3. Choose any app (text, email, etc.)
4. Your contact info is shared as text

---

## Adding Contacts

### Scanning a QR Code

1. Open Mycel
2. Go to **Contacts** tab
3. Tap the QR scanner icon
4. Point your camera at their QR code
5. Contact is added automatically

After scanning:

- Their info appears in your contacts
- You can immediately start a conversation

### Manual Entry

If you received a UID via text:

1. Go to **Contacts** tab
2. Tap **Add Contact** or **+**
3. Enter their Device UID
4. Optionally add a nickname
5. Tap **Save**

!!! tip "Verify the UID"
    Make sure you've entered the UID correctly. A typo means messages go nowhere.

---

## Contact Discovery

### Automatic Discovery

When you're near other Mycel users:

1. Devices detect each other via Bluetooth/Wi-Fi
2. Public profiles are exchanged
3. New contacts appear in your list with a badge

You don't need to explicitly add nearby users - they're discovered automatically.

### Discovery vs Contacts

| | Discovered Peers | Saved Contacts |
|-|------------------|----------------|
| Added automatically | Yes | No |
| Persist after leaving range | Temporarily | Yes |
| Can message | Yes | Yes |
| Shows in Contacts | Yes (as neighbors) | Yes |

### New Contact Badge

When new contacts are discovered:

- A badge appears on the Contacts tab
- New contacts are highlighted in the list
- Badge clears when you view the contacts

---

## Managing Contacts

### Viewing Contact Profiles

1. Go to **Contacts**
2. Tap a contact's name or avatar
3. View their profile:
   - Display name
   - Device UID
   - Public key fingerprint
   - Last seen time
   - Online status

### Renaming Contacts

You can set a local nickname that only you see:

1. Open the contact's profile
2. Tap **Rename** or the edit icon
3. Enter your preferred name
4. Save

The nickname:

- Only appears on your device
- Doesn't change their actual name
- Helps you identify contacts

### Contact Status

| Status | Meaning |
|--------|---------|
| **Online** | Connected within last 15 seconds |
| **Offline** | Not currently connected |
| **Last seen** | Time of last connection |

### Searching Contacts

In the Contacts tab:

1. Use the search bar at the top
2. Search by:
   - Name/nickname
   - Device UID
   - Fingerprint

---

## Starting Conversations

### From Contacts

1. Go to **Contacts**
2. Tap a contact
3. Tap **Message** or the chat icon
4. Start typing

### From Contact Profile

1. Open the contact's profile
2. Tap **Start Chat** or **Message**

### Quick Actions

- Long-press a contact for quick actions
- Swipe for common operations (varies by device)

---

## Contact Sync

### How It Works

Contact information syncs through the mesh:

1. When you meet someone, profiles are exchanged
2. Profile updates (name changes) propagate
3. Deleted contacts stay deleted locally

### What Syncs

- Display names
- Public keys
- Optional location hints
- Profile sequence numbers

### What Doesn't Sync

- Your local nicknames
- Message history
- Contact groupings/organization

---

## Privacy

### What Contacts See About You

When you're nearby, others can see:

- Your display name
- Your Device UID
- Your public key
- That you're running Mycel
- Approximate location (you're "nearby")

### What You Control

- **Display name** - Set whatever you're comfortable sharing
- **Full name** - Optional, can leave blank
- **Location sharing** - Opt-in for geo-routing (city-level, not precise)

### Recommendations

1. **Use a nickname** - Don't use your real name if privacy-conscious
2. **Review settings** - Check what you're sharing in Profile settings
3. **Be aware** - Nearby discovery means strangers know you're there

---

## Troubleshooting

### "Contact not found"

- **Check the UID** - Make sure you entered it correctly
- **Check connectivity** - You may need to be in range first
- **Try scanning QR** - More reliable than manual entry

### "Can't scan QR code"

- **Check camera permission** - Mycel needs camera access
- **Improve lighting** - QR codes need good visibility
- **Hold steady** - Keep the phone still while scanning
- **Check distance** - Not too close, not too far

### "Contact shows offline"

- **They may not be nearby** - Offline is normal when not in range
- **Check your connectivity** - Go to Mesh Lab > Nearby
- **Be patient** - Status updates when you reconnect

### "I accidentally added wrong contact"

- You can remove contacts from your list
- Find them in Contacts, open profile, look for Remove/Delete option
- Message history remains but is archived

### "Duplicate contacts"

Can happen if someone:

- Reinstalled the app (new identity)
- Has multiple devices

Each device generates a unique identity, so the same person on two devices = two contacts.

---

## Contact List Organization

### Pinning Conversations

Important contacts can be pinned:

1. Go to **Chats** > **Direct Messages**
2. Swipe right on a conversation
3. Tap **Pin**

Pinned conversations stay at the top.

### Archiving Conversations

To hide inactive conversations:

1. Swipe left on a conversation
2. Tap **Archive**

Archived conversations move to the Archive tab.

### Finding Archived Contacts

1. Go to **Chats**
2. Tap **Archived** tab
3. View and restore as needed

---

## Technical Details

### Key Types

| Key | Algorithm | Purpose |
|-----|-----------|---------|
| Identity | Ed25519 | Signing, authentication |
| Encryption | X25519 | Key agreement for DMs |

### Profile Data

Each contact profile includes:

- Public key (Ed25519)
- Display name
- Full name (optional)
- Device label
- Location hint (H3 cell, optional)
- Profile sequence number
- Last updated timestamp

**Deep Dive:** [Keys & Cryptography](../deep-dive/identity/keys.md)

---

**Next:** [Messaging](messaging.md) | [Settings](settings.md) | [Groups](groups.md)
