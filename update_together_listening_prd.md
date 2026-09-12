# Her Music — Together Listening PRD

## 1. Feature
Feature name: **Together Listening**

Purpose: Allow two people to join a private Her Music listening room using a unique invite code or shareable invitation link and listen to the same track in synchronized playback.

The feature must feel like a natural extension of Her Music, not a generic watch-party product.

Core concept:

> Create a private room → share the invitation → partner joins → both listen together in real time.

## 2. Goals

- Create a private two-person listening room.
- Generate a unique room code.
- Generate a shareable invitation URL.
- Allow joining through either code or link.
- Synchronize playback state in near real time.
- Show both participants' presence.
- Support a shared queue.
- Keep the existing Her Music Liquid Glass visual language.
- Work across desktop, tablet, and phone.
- Keep audio playback local on each participant's device; synchronize state rather than streaming one user's audio to the other.

## 3. Non-Goals for V1

Do not add:
- video calling
- voice calling
- public rooms
- large group rooms
- general-purpose chat
- social feed
- complex moderation systems

V1 is intentionally private and focused on two-person listening.

## 4. Room Model

A room has:
- room ID
- human-readable unique invite code
- invite token/link
- host
- guest
- current track
- playback state
- queue
- playback position
- synchronization timestamp
- room status
- created/updated timestamps

Maximum participants: 2.

Recommended states:
- waiting
- active
- ended
- expired

## 5. User Flow

### Host
1. Open Her Music.
2. Select **♡ Together**.
3. Select **Create Listening Room**.
4. System generates room code and invitation link.
5. Host copies/shares the invitation.
6. Waiting screen shows `Waiting for Sweetie ♡`.
7. Guest joins.
8. Both clients receive synchronized room state.
9. Listening begins.

### Guest
1. Open invitation link OR Together page.
2. If necessary, enter room code.
3. Confirm display name/profile.
4. Join room.
5. Receive current track and playback state.
6. Local audio is synchronized with host/room state.

## 6. Playback Rules

V1 default: host-authoritative playback.

Host can:
- play
- pause
- seek
- previous
- next
- change queue
- reorder queue
- toggle shuffle
- toggle repeat

Guest can:
- view playback
- favorite songs
- add songs to shared queue
- optionally request/trigger queue actions if enabled by host

Design the backend so a future shared-control mode can be added without rewriting the synchronization protocol.

## 7. Synchronization

Synchronize:
- track ID
- playing/paused state
- playback position
- server timestamp
- queue
- current queue index
- shuffle state
- repeat state

Clients calculate expected playback position using timestamps rather than relying on repeated position broadcasts.

Do not stream raw audio through the WebSocket.

## 8. Existing Her Music Integration

Reuse:
- existing song data model
- player state
- MiniPlayer
- FullScreenPlayer
- NowPlaying
- responsive layouts
- Liquid Glass components
- time/environment/weather system

Add:
- Together navigation item
- Together room page
- participant presence
- shared queue state
- synchronization status
- invite UI

## 9. UI Copy

Primary:
`♡ Together Listening`

Create:
`Create a private listening room`

Waiting:
`Waiting for Sweetie ♡`

Joined:
`Sweetie joined your listening room ♡`

Sync:
`Listening together`

Disconnected:
`Reconnecting…`

Room:
`Room Code`

Invite:
`Share Invitation`

Leave:
`Leave Together`

## 10. Responsive Design

Desktop:
- Together appears in sidebar navigation.
- Main room uses a large Liquid Glass room panel.
- Participant cards can sit beside Now Playing.
- Shared queue is visible below/alongside the player.

Tablet:
- Together appears in compact navigation rail.
- Room content becomes stacked/compact.
- Player remains accessible.

Mobile:
- Together appears in bottom navigation or an overflow/navigation destination.
- Room becomes a dedicated full-width page.
- Mini-player remains persistent.
- Tapping player opens FullScreenPlayer.
- Participant presence remains visible.
- Share invitation uses a touch-friendly action sheet.

No horizontal overflow.

## 11. Security

- Room codes must be unpredictable enough to prevent guessing.
- Invitation tokens should be cryptographically random.
- Server validates membership for every room WebSocket connection.
- A room must never expose another room's state.
- Enforce a maximum of two active participants.
- Expire inactive rooms.
- Never trust client-provided host status.
- Server is authoritative for membership and playback permissions.

## 12. Acceptance Criteria

- A host can create a room.
- A unique code is generated.
- A unique invitation link is generated.
- Guest can join using link.
- Guest can join using code.
- Maximum two participants is enforced.
- Presence updates in real time.
- Track state synchronizes.
- Play/pause synchronizes.
- Seek synchronizes.
- Next/previous synchronizes.
- Queue synchronizes.
- Reconnection restores current state.
- Leaving a room removes presence.
- Existing music functionality continues working.
- Desktop/tablet/mobile layouts work.
- Build and type checks pass.
