# Her Music — Together Listening UI Flow

## 1. Navigation

Add:

```text
♡ Together
```

to the existing Her Music navigation.

Desktop:
- sidebar item

Tablet:
- navigation rail item

Mobile:
- bottom navigation or secondary navigation destination

Keep the existing Her Music Liquid Glass visual language.

## 2. Together Landing Page

Title:

```text
♡ Together Listening
```

Subtitle:

```text
Listen to the same song, together.
```

Primary action:

```text
Create Listening Room
```

Secondary action:

```text
Join with Code
```

Optional recent room section can be added later.

## 3. Create Room Flow

User selects:

```text
Create Listening Room
```

UI:

```text
♡ Create Together Room

A private room for you and Sweetie.

[ Create Room ]

Private • 2 people maximum
```

After creation:

```text
♡ Your Listening Room

Room Code

SATHI-X7K92

[ Copy Code ]

[ Share Invitation ]

Waiting for Sweetie ♡

● You — Host
○ Waiting for Sweetie
```

## 4. Invitation

The share panel should contain:

```text
♡ Join me on Her Music

Let's listen together.

Room: SATHI-X7K92

[ Copy Invitation Link ]

[ Share ]

[ QR Code ]   optional
```

The URL should contain the secure invitation token, not merely a guessable room code.

Example:

```text
/her-music/together/invite/<secure-token>
```

## 5. Guest Join Flow

When guest opens the invitation:

```text
♡ You've been invited

to listen together

Sweetie (Sathi)
+
You

[ Join Listening Room ]
```

If not authenticated, route through the existing authentication flow.

If already authenticated:
- join directly after confirmation.

## 6. Room Active State

Desktop layout:

```text
┌───────────────────────────────────────────────────────────┐
│ ♡ Together Listening                    ● Connected       │
├───────────────────────────────────────────────────────────┤
│                                                           │
│       You                         Sweetie                 │
│      ● Online                     ● Online                │
│                                                           │
│              ┌──────────────┐                             │
│              │  Album Art   │                             │
│              └──────────────┘                             │
│                                                           │
│                 Tu Jo Mila                                │
│                  Stebin Ben                               │
│                                                           │
│              ───●────────────                           │
│                                                           │
│             ◀     ❚❚     ▶                               │
│                                                           │
├───────────────────────────────────────────────────────────┤
│ Shared Queue                                               │
│                                                           │
│  Tu Jo Mila                                                │
│  Raatan Lambiyan                                           │
│  Tomake Chai                                               │
└───────────────────────────────────────────────────────────┘
```

Use existing NowPlaying/Player components wherever practical.

## 7. Participant Presence

Display two compact participant identities:

```text
● You
● Sweetie
```

States:
- Online
- Connecting
- Offline

When joining:

```text
♡ Sweetie joined your listening room
```

Use a subtle animation.

Do not create intrusive notifications.

## 8. Synchronization Indicator

Show a small status:

```text
♡ Listening together
```

or:

```text
● Synced
```

If reconnecting:

```text
↻ Reconnecting…
```

If temporarily out of sync:

```text
↻ Syncing…
```

## 9. Shared Queue

Display:

```text
Shared Queue

01  Tu Jo Mila
02  Raatan Lambiyan
03  Tomake Chai
04  Perfect
```

Guest can add songs.

Host controls queue playback in V1.

## 10. Mobile Room

Mobile should be intentionally designed, not a compressed desktop page.

Top:

```text
‹ Together          ● Synced
```

Center:

```text
Sweetie + You

[ Large Album Art ]

Tu Jo Mila
Stebin Ben
```

Player controls:

```text
♡
◀
❚❚
▶
↻
```

Bottom:

```text
Shared Queue
```

Persistent mini-player remains available.

## 11. Leave Room

Menu:

```text
Together Options

[ Share Invitation ]
[ Copy Room Code ]
[ Leave Together Room ]
```

Host leaving:

```text
End this listening room?

[ Cancel ] [ End Room ]
```

Guest leaving:

```text
Leave listening room?

[ Cancel ] [ Leave ]
```

## 12. Full Player

When the user taps the player:

- open the existing FullScreenPlayer
- preserve Together status
- show participant presence
- show `♡ Listening together`
- playback remains synchronized

Do not create a completely separate music player implementation.

## 13. Visual Design

Follow existing Her Music:
- Liquid Glass
- approximately 50% glass transparency
- backdrop blur
- translucent borders
- pink/coral accents
- deep navy/purple environment
- subtle highlights
- premium Apple-inspired iconography
- smooth motion
- responsive behavior

Together should feel like it belongs to the same application.

## 14. Empty/Waiting States

Waiting:

```text
♡ Waiting for Sweetie

Share the invitation and start listening together.

[ Share Invitation ]
```

No queue:

```text
Your shared queue is empty.

[ Add a Song ]
```

Room expired:

```text
This listening room has ended.

[ Create New Room ]
```

Room full:

```text
This private room is already occupied.

Together Listening supports two people.
```

## 15. Accessibility

- Keyboard navigable.
- Proper button labels.
- Visible focus states.
- Screen-reader labels.
- Minimum touch target around 44px.
- Respect reduced motion.
- Maintain sufficient contrast through the glass.

## 16. UX Principle

The feature should communicate one simple feeling:

> “We are listening to the same song at the same time.”

Avoid turning it into a social network.

Keep the experience:
- private
- intimate
- simple
- fast
- romantic
- premium
