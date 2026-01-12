# What Mycel Can (and Can't) Do

Understanding what Mycel is designed for helps set the right expectations.

## What Works Now

| Feature | Description |
|---------|-------------|
| **Send messages without internet** | Uses Bluetooth and WiFi between nearby phones |
| **End-to-end encryption** | Only you and the recipient can read messages |
| **No phone number required** | Privacy-preserving identity system |
| **Private groups** | Invite-only encrypted group messaging |
| **Works during shutdowns** | Designed for no-internet scenarios |
| **Background delivery** | Keeps working when app isn't open |

## What Takes Time

Mycel is a **Delay-Tolerant Network** - delivery can take time. This is normal, not an error.

| Scenario | Expected Time |
|----------|---------------|
| Same room/building | Seconds to minutes |
| Same neighborhood | Minutes to hours |
| Same city | Hours |
| Different cities | Hours to days |
| Remote areas | Days or longer |

!!! info "Why so slow?"
    Messages travel through **people**, not cables. Each hop requires physical proximity between Mycel users.

## What Mycel Can't Do

These are **not bugs** - they're fundamental properties of mesh networking:

| Limitation | Reason |
|------------|--------|
| **Guarantee delivery** | If no chain of users exists, messages wait |
| **Instant messaging** | Store-and-forward takes time |
| **Work on all phones** | Requires Android 10+ with Google Play Services |
| **Delivery to iPhone** | iOS app not yet available |

## Technical Requirements

| Requirement | Details |
|-------------|---------|
| **Android version** | Android 10 (API 29) or higher |
| **Google Play Services** | Required for Nearby Connections |
| **Bluetooth** | Must be enabled |
| **Battery optimization** | Must be disabled for Mycel |

!!! warning "Huawei devices (post-2019)"
    Most Huawei devices manufactured after 2019 lack Google Play Services and cannot run Mycel.

## Not For Emergencies

!!! danger "Important"
    **Do not rely on Mycel for emergency communications.**

    - Delivery can take hours or days
    - No guarantee messages will arrive
    - For emergencies, use official emergency services

## Cold Start Problem

When Mycel first launches in a region:

- **Few carriers exist** - Messages take longer to deliver
- **Early adopters are building the network** - Every user helps
- **Patience is required** - Delivery improves as the network grows

The more people who join, the faster and more reliable delivery becomes.

## Coming Soon

These features are planned but not yet available:

| Feature | Status |
|---------|--------|
| Channels (broadcast) | In progress |
| iOS app | Planned |
| Desktop app | Planned |
| LoRa transport | Proposed |

See the [Roadmap](../deep-dive/roadmap.md) for details.
