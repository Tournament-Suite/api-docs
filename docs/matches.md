# Matches API

Base path: `/api/v1/projects/{projectId}/tournaments/{tournamentId}/matches`

## Match States

| State | Value |
|---|---|
| Pending | `pending` |
| Ready | `ready` |
| In Progress | `in_progress` |
| Awaiting Score | `awaiting_score` |
| Under Review | `under_review` |
| Completed | `completed` |
| Cancelled | `cancelled` |

## Endpoints

### List matches
```http
GET /api/v1/projects/{projectId}/tournaments/{tournamentId}/matches
```

Query params: `status`, `round`, `page`, `limit`

### Get match
```http
GET /api/v1/projects/{projectId}/tournaments/{tournamentId}/matches/{matchId}
```

**Response:**
```json
{
  "id": "match-uuid",
  "round": 1,
  "status": "in_progress",
  "scheduledAt": "2026-06-24T15:00:00Z",
  "participants": [
    { "id": "participant-uuid-1", "name": "Team Alpha", "seed": 1 },
    { "id": "participant-uuid-2", "name": "Team Beta", "seed": 2 }
  ],
  "scores": [],
  "winnerId": null
}
```

### Update match schedule
```http
PATCH /api/v1/projects/{projectId}/tournaments/{tournamentId}/matches/{matchId}
```

```json
{
  "scheduledAt": "2026-06-24T16:00:00Z",
  "server": "cs2-server-uuid"
}
```

### Submit score
```http
POST /api/v1/projects/{projectId}/tournaments/{tournamentId}/matches/{matchId}/scores
```

```json
{
  "participantId": "participant-uuid-1",
  "score": 16,
  "proof": "https://cdn.example.com/screenshots/round1.png"
}
```

**Response:**
```json
{
  "matchId": "match-uuid",
  "status": "awaiting_score",
  "scores": [
    { "participantId": "participant-uuid-1", "score": 16, "submittedAt": "2026-06-24T15:45:00Z" }
  ]
}
```

### Override result (organizer only)
```http
POST /api/v1/projects/{projectId}/tournaments/{tournamentId}/matches/{matchId}/override
```

```json
{
  "winnerId": "participant-uuid-1",
  "reason": "Opponent forfeited"
}
```

### Create dispute
```http
POST /api/v1/projects/{projectId}/tournaments/{tournamentId}/matches/{matchId}/disputes
```

```json
{
  "reason": "incorrect_score",
  "description": "Opponent submitted wrong round score.",
  "evidence": ["https://cdn.example.com/screenshots/evidence1.png"]
}
```

**Response:**
```json
{
  "id": "dispute-uuid",
  "matchId": "match-uuid",
  "status": "open",
  "reason": "incorrect_score",
  "createdAt": "2026-06-24T15:50:00Z"
}
```

### Resolve dispute (organizer only)
```http
PATCH /api/v1/projects/{projectId}/tournaments/{tournamentId}/matches/{matchId}/disputes/{disputeId}
```

```json
{
  "resolution": "upheld",
  "winnerId": "participant-uuid-1",
  "notes": "Score evidence confirmed."
}
```

### Get match chat
```http
GET /api/v1/projects/{projectId}/tournaments/{tournamentId}/matches/{matchId}/chat
```