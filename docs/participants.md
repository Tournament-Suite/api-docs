# Participants API

Base path: `/api/v1/projects/{projectId}/tournaments/{tournamentId}/participants`

## Participant States

| State | Value |
|---|---|
| Pending Approval | `pending` |
| Approved | `approved` |
| Checked In | `checked_in` |
| Eliminated | `eliminated` |
| Disqualified | `disqualified` |
| Withdrawn | `withdrawn` |

## Participant Types

| Type | Value |
|---|---|
| Solo Player | `solo` |
| Team | `team` |

## Endpoints

### List participants
```http
GET /api/v1/projects/{projectId}/tournaments/{tournamentId}/participants
```

Query params: `status`, `type`, `page`, `limit`

### Register participant
```http
POST /api/v1/projects/{projectId}/tournaments/{tournamentId}/participants
```

```json
{
  "type": "team",
  "teamId": "team-uuid",
  "roster": ["user-uuid-1", "user-uuid-2", "user-uuid-3", "user-uuid-4", "user-uuid-5"],
  "seedRequest": null
}
```

**Response:**
```json
{
  "id": "participant-uuid",
  "tournamentId": "tournament-uuid",
  "type": "team",
  "teamId": "team-uuid",
  "status": "pending",
  "seed": null,
  "registeredAt": "2026-06-21T10:00:00Z"
}
```

### Get participant
```http
GET /api/v1/projects/{projectId}/tournaments/{tournamentId}/participants/{participantId}
```

### Approve participant (organizer only)
```http
POST /api/v1/projects/{projectId}/tournaments/{tournamentId}/participants/{participantId}/approve
```

### Reject participant (organizer only)
```http
POST /api/v1/projects/{projectId}/tournaments/{tournamentId}/participants/{participantId}/reject
```

```json
{
  "reason": "Roster requirements not met."
}
```

### Check in participant
```http
POST /api/v1/projects/{projectId}/tournaments/{tournamentId}/participants/{participantId}/checkin
```

Check-in window is enforced server-side based on tournament `checkInStart` / `checkInEnd` fields.

### Bulk check-in (organizer only)
```http
POST /api/v1/projects/{projectId}/tournaments/{tournamentId}/participants/checkin/bulk
```

```json
{
  "participantIds": ["participant-uuid-1", "participant-uuid-2"]
}
```

### Disqualify participant (organizer only)
```http
POST /api/v1/projects/{projectId}/tournaments/{tournamentId}/participants/{participantId}/disqualify
```

```json
{
  "reason": "cheating",
  "notes": "Confirmed wallhack via anti-cheat service."
}
```

### Withdraw participant
```http
POST /api/v1/projects/{projectId}/tournaments/{tournamentId}/participants/{participantId}/withdraw
```

Participants may self-withdraw before the tournament starts. Organizers may withdraw any participant at any time.

### Update seed (organizer only)
```http
PATCH /api/v1/projects/{projectId}/tournaments/{tournamentId}/participants/{participantId}
```

```json
{
  "seed": 3
}
```