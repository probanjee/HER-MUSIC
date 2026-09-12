# Her Music — Together Listening Database Schema

Use PostgreSQL/Supabase-compatible SQL.

## 1. Profiles

Reuse the existing application user/profile table if one already exists.

Conceptual fields:
- id UUID PRIMARY KEY
- display_name TEXT
- avatar_url TEXT
- created_at TIMESTAMPTZ
- updated_at TIMESTAMPTZ

Do not create a duplicate profile system if Her Music already has one.

## 2. listening_rooms

```sql
CREATE TABLE listening_rooms (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    room_code VARCHAR(16) NOT NULL UNIQUE,
    invite_token TEXT NOT NULL UNIQUE,
    host_user_id UUID NOT NULL REFERENCES profiles(id),
    status VARCHAR(16) NOT NULL DEFAULT 'waiting',
    current_song_id UUID NULL,
    current_queue_index INTEGER NOT NULL DEFAULT 0,
    is_playing BOOLEAN NOT NULL DEFAULT FALSE,
    position_seconds DOUBLE PRECISION NOT NULL DEFAULT 0,
    state_version BIGINT NOT NULL DEFAULT 0,
    state_changed_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    expires_at TIMESTAMPTZ
);

CREATE INDEX idx_listening_rooms_host
ON listening_rooms(host_user_id);

CREATE INDEX idx_listening_rooms_status
ON listening_rooms(status);

CREATE INDEX idx_listening_rooms_expires
ON listening_rooms(expires_at);
```

`room_code` is for manual entry.

`invite_token` is for shareable links.

## 3. listening_room_members

```sql
CREATE TABLE listening_room_members (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    room_id UUID NOT NULL REFERENCES listening_rooms(id) ON DELETE CASCADE,
    user_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
    role VARCHAR(16) NOT NULL,
    joined_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    last_seen_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    is_online BOOLEAN NOT NULL DEFAULT TRUE,

    UNIQUE(room_id, user_id)
);
```

Roles:
- host
- guest

Application/server logic must enforce a maximum of two members per room.

## 4. listening_room_queue

```sql
CREATE TABLE listening_room_queue (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    room_id UUID NOT NULL REFERENCES listening_rooms(id) ON DELETE CASCADE,
    song_id UUID NOT NULL,
    position INTEGER NOT NULL,
    added_by UUID NOT NULL REFERENCES profiles(id),
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

    UNIQUE(room_id, position)
);

CREATE INDEX idx_room_queue
ON listening_room_queue(room_id, position);
```

Reuse the existing songs/tracks table instead of duplicating song metadata.

## 5. Optional room playback events

Do NOT store every playback tick.

If audit/debug history is needed, store only meaningful events:

```sql
CREATE TABLE listening_room_events (
    id BIGSERIAL PRIMARY KEY,
    room_id UUID NOT NULL REFERENCES listening_rooms(id) ON DELETE CASCADE,
    user_id UUID REFERENCES profiles(id),
    event_type VARCHAR(32) NOT NULL,
    payload JSONB,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_room_events
ON listening_room_events(room_id, created_at);
```

Examples:
- room_created
- member_joined
- member_left
- play
- pause
- seek
- song_changed
- queue_updated
- room_ended

Do not write continuous `timeupdate` events to the database.

## 6. Security/RLS Direction

If using Supabase:
- Users may read rooms they belong to.
- Users may read members of rooms they belong to.
- Users may read/update queue items only for rooms they belong to.
- Host-only operations must be enforced server-side.
- Never rely solely on client-side checks for host privileges.
- Invitation token should not grant unrestricted database access by itself.

## 7. State Model

Authoritative room state:

```text
room_id
current_song_id
current_queue_index
is_playing
position_seconds
state_changed_at
state_version
shuffle
repeat
```

The client derives the current position from:

```text
position =
  position_seconds +
  (current_server_time - state_changed_at)
```

when `is_playing = true`.

This avoids broadcasting playback position every frame.
