# Battery & Background Behavior

Mycel needs to run in the background to relay messages. Here's what to expect and how to optimize.

## Why Background Operation Matters

Unlike traditional messengers that rely on push notifications from a server, Mycel must:

- **Continuously scan** for nearby devices
- **Maintain connections** with encountered peers
- **Store and forward** messages for others

If Mycel stops running, your phone drops out of the mesh network.

## Battery Impact

| Usage Pattern | Battery Impact | Relay Effectiveness |
|---------------|----------------|---------------------|
| App in foreground | High | Best |
| App in background (unrestricted) | Medium | Good |
| App in background (restricted) | Low | Poor |
| App killed by system | None | None |

**Typical consumption:** 3-8% of daily battery depending on activity and number of nearby peers.

## Android's Background Restrictions

Modern Android aggressively kills background apps. You need to configure your phone to let Mycel run:

### 1. Disable Battery Optimization

**Settings > Apps > Mycel > Battery > Unrestricted**

This tells Android not to limit Mycel's background activity.

### 2. Lock the App (Some Phones)

Some manufacturers (Xiaomi, Huawei, Samsung) have extra battery management:

| Brand | Where to Find |
|-------|---------------|
| **Xiaomi** | Settings > Battery > App battery saver > Mycel > No restrictions |
| **Huawei** | Settings > Battery > App launch > Mycel > Manage manually > Enable all |
| **Samsung** | Settings > Device care > Battery > App power management > Mycel > Don't optimize |
| **OnePlus** | Settings > Battery > Battery optimization > Mycel > Don't optimize |

!!! tip "Don't Kill My App"
    Visit [dontkillmyapp.com](https://dontkillmyapp.com) for device-specific instructions.

### 3. Allow Background Location

**Settings > Apps > Mycel > Permissions > Location > Allow all the time**

Without this, Bluetooth scanning stops when you lock your phone.

## What Happens When...

### Phone Goes to Sleep

With proper settings, Mycel continues scanning and relaying. The app icon stays in the notification bar.

### Battery Saver Mode

Most battery saver modes will restrict Mycel. Consider adding Mycel to exceptions, or disable battery saver during important communication periods.

### Phone Restarts

Mycel automatically starts when your phone boots. You'll see the notification icon appear.

### App Is Force-Stopped

Force-stopping completely kills Mycel. You must manually reopen the app to rejoin the mesh.

## Battery Optimization Tips

### If You Need Maximum Battery Life

1. Use Mycel primarily when you need it (emergencies, events)
2. Close the app when returning to internet connectivity
3. Reduce scanning intensity in Settings (future feature)

### If You Want Best Mesh Relay

1. Keep Mycel running at all times
2. Keep phone charged or carry a power bank
3. Grant all permissions and disable optimizations
4. Don't use battery saver mode

## Notifications

Mycel shows a persistent notification when running in background:

> **Mycel is running**
> Mesh relay active

This notification:

- Indicates the app is working
- Prevents Android from killing the app
- Shows current connection status

You can minimize this notification, but don't disable it entirely.

## Troubleshooting

### "Mycel keeps stopping"

1. Check battery optimization settings
2. Check manufacturer-specific settings
3. Exclude Mycel from battery saver
4. Don't use "Clean" or "Boost" apps that kill background services

### "Not receiving messages when screen is off"

1. Ensure background location is "Allow all the time"
2. Check persistent notification is visible
3. Verify battery optimization is disabled

### "High battery usage"

1. This is normal if there are many nearby peers
2. Reduce scanning interval (future feature)
3. Use only when needed if battery is critical

---

**See Also:** [Permissions](../getting-started/permissions.md) | [How Delivery Works](delivery.md)
