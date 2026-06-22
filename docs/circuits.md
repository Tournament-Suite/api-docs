# Circuits API

Base path: `/api/v1/projects/{projectId}/circuits`

## Overview

Circuits group multiple tournaments into a season-long competitive series. Points accumulate across events; top qualifiers advance to the circuit final.

## Circuit States

| State | Value |
|---|---|
| Draft | `draft` |
| Active | `active` |
| Completed | `completed` |
| Archived | `archived` |

## Endpoints

### List circuits
```http
GET /api/v1/projects/{projectId}/circuits
```

Query params: `status`, `game`, `season`, `page`, `limit`

### Create circuit
```http
POST /api/v1/projects/{projectId}/circuits
```

```json
{
  "name": "Pro Series Season 3",
  "game": "cs2",
  "season": "2026",
  "description": "Six-event LAN qualifier series leading to the Grand Final.",
  "pointsScheme": "standard",
  "qualificationSlots": 8,
  "startDate": "2026-03-01T00:00:00Z",
  "endDate": "2026-09-30T23:59:59Z"
}
```

**Response:**
```json
{
  "id": "circuit-uuid",
  "name": "Pro Series Season 3",
  "game": "cs2",
  "season": "2026",
  "status": "draft",
  "pointsScheme": "standard",
  "qualificationSlots": 8,
  "createdAt": "2026-06-01T00:00:00Z"
}
```

### Get circuit
```http
GET /api/v1/projects/{projectId}/circuits/{circuitId}
```

### Update circuit
```http
PATCH /api/v1/projects/{projectId}/circuits/{circuitId}
```

### Delete circuit (draft only)
```http
DELETE /api/v1/projects/{projectId}/circuits/{circuitId}
```

### Publish circuit
```http
POST /api/v1/projects/{projectId}/circuits/{circuitId}/publish
```

### Add tournament to circuit
```http
POST /api/v1/projects/{projectId}/circuits/{circuitId}/events
```

```json
{
  "tournamentId": "tournament-uuid",
  "pointsMultiplier": 1.5,
  "order": 2
}
```

### Remove tournament from circuit
```http
DELETE /api/v1/projects/{projectId}/circuits/{circuitId}/events/{tournamentId}
```

### Get circuit standings
```http
GET /api/v1/projects/{projectId}/circuits/{circuitId}/standings
```

Query params: `page`, `limit`

**Response:**
```json
{
  "circuitId": "circuit-uuid",
  "season": "2026",
  "updatedAt": "2026-06-22T12:00:00Z",
  "standings": [
    {
      "rank": 1,
      "participantId": "participant-uuid",
      "name": "Team Alpha",
      "points": 1450,
      "eventsPlayed": 4,
      "qualified": true
    }
  ]
}
```

### Get qualified participants
```http
GET /api/v1/projects/{projectId}/circuits/{circuitId}/qualified
```

Returns participants who have secured a qualification slot.

### Update points scheme
```http
PUT /api/v1/projects/{projectId}/circuits/{circuitId}/points-scheme
```

```json
{
  "placements": [
    { "place": 1, "points": 500 },
    { "place": 2, "points": 350 },
    { "place": 3, "points": 250 },
    { "place": 4, "points": 175 },
    { "place": 5, "points": 100 }
  ]
}
```