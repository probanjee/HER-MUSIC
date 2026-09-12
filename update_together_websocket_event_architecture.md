# Her Music — Together Listening WebSocket Architecture

## 1. Transport

Use WebSocket or Socket.IO.

Recommended logical channel:

```text
/together/{roomId}
```

Every connection must be authenticated.

The server validates:
- user identity
- room membership
- room status
- participant limit
- permissions

## 2. Connection Flow

```text
Client
  ↓
Authenticate
  ↓
Join room
  ↓
Server validates membership
  ↓
Server sends room_state
  ↓
Client synchronizes local player
  ↓
Presence heartbeat
  ↓
Playback events
  ↓
State updates
```

## 3. Server → Client Events

### room_state

Sent immediately after joining.

```json
{
  "type": "room_state",
  "roomId": "uuid",
  "currentSongId": "uuid",
  "queueIndex": 0,
  "isPlaying": true,
  "positionSeconds": 42.4,
  "stateChangedAt": "timestamp",
  "stateVersion": 17,
  "hostUserId": "uuid",
  "members": []
}
```

### member_joined

```json
{
  "type": "member_joined",
  "user": {
    "id": "uuid",
    "displayName": "Sweetie (Sathi)",
    "avatarUrl": "..."
  }
}
```

### member_left

```json
{
  "type": "member_left",
  "userId": "uuid"
}
```

### playback_changed

```json
{
  "type": "playback_changed",
  "action": "play",
  "songId": "uuid",
  "positionSeconds": 42.4,
  "serverTimestamp": "timestamp",
  "stateVersion": 18
}
```

Actions:
- play
- pause
- seek
- song_changed

### queue_updated

```json
{
  "type": "queue_updated",
  "queue": []
}
```

### presence_updated

```json
{
  "type": "presence_updated",
  "userId": "uuid",
  "online": true
}
```

### room_ended

```json
{
  "type": "room_ended",
  "reason": "host_left"
}
```

### error

```json
{
  "type": "error",
  "code": "ROOM_FULL",
  "message": "This listening room already has two participants."
}
```

## 4. Client → Server Events

### join_room

```json
{
  "type": "join_room",
  "roomId": "uuid"
}
```

### play

```json
{
  "type": "play",
  "positionSeconds": 42.4
}
```

### pause

```json
{
  "type": "pause",
  "positionSeconds": 42.4
}
```

### seek

```json
{
  "type": "seek",
  "positionSeconds": 88.2
}
```

### change_song

```json
{
  "type": "change_song",
  "songId": "uuid"
}
```

### queue_add

```json
{
  "type": "queue_add",
  "songId": "uuid"
}
```

### queue_remove

```json
{
  "type": "queue_remove",
  "queueItemId": "uuid"
}
```

### queue_reorder

```json
{
  "type": "queue_reorder",
  "queueItemId": "uuid",
  "newPosition": 2
}
```

### heartbeat

```json
{
  "type": "heartbeat"
}
```

## 5. Host Authorization

For V1:

```text
HOST
 ├── play
 ├── pause
 ├── seek
 ├── next
 ├── previous
 ├── change song
 ├── reorder queue
 ├── shuffle
 └── repeat

GUEST
 ├── view playback
 ├── add queue item
 └── favorite songs
```

The server must reject unauthorized events.

Do not trust a client saying it is the host.

## 6. Playback Synchronization

Do NOT broadcast:

```text
currentTime
currentTime
currentTime
...
```

every animation frame.

Instead broadcast meaningful state changes.

Example:

```text
Host presses PLAY
      ↓
Server stores:
song = X
position = 52.1
playing = true
timestamp = T
      ↓
Broadcast playback_changed
      ↓
Guest calculates current position
      ↓
Guest starts local audio
```

## 7. Drift Correction

Clients should periodically compare their expected playback position with the authoritative room position.

If drift is tiny:
- gradually adjust playback rate or leave unchanged.

If drift is significant:
- seek to the authoritative position.

Do not repeatedly hard-seek for tiny differences.

## 8. Reconnection

When WebSocket disconnects:

```text
CONNECTED
 ↓
RECONNECTING
 ↓
CONNECTED
 ↓
REQUEST/RECEIVE room_state
 ↓
RESYNC PLAYER
```

Never assume the previous client state is still authoritative.

## 9. Race Conditions

Use `state_version`.

Example:

```text
version 17
version 18
version 19
```

Ignore stale events.

Server increments the version whenever authoritative playback state changes.

## 10. Server Authority

The server is authoritative for:
- membership
- room status
- host role
- playback state
- queue state
- state version

The browser is authoritative only for:
- local audio rendering
- local UI state
- local buffering status

## 11. Room Lifecycle

```text
WAITING
   ↓ guest joins
ACTIVE
   ↓ host ends/leaves
ENDED
```

Inactive rooms can become:

```text
EXPIRED
```

after the configured inactivity period.

## 12. Important Rule

Audio itself is NEVER sent through WebSocket.

WebSocket synchronizes metadata and playback state.

Each client loads the same authorized audio source independently.
