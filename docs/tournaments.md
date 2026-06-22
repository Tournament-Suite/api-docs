# Tournaments API

Base path: `/api/v1/projects/{projectId}/tournaments`

## Formats

| Format | Value |
|---|---|
| Single Elimination | `single_elimination` |
| Double Elimination | `double_elimination` |
| Swiss | `swiss` |
| Round Robin | `round_robin` |
| Group Stage | `group_stage` |
| Gauntlet | `gauntlet` |
| Free For All | `free_for_all` |

## Endpoints

### List tournaments
```http
GET /api/v1/projects/{projectId}/tournaments
```

Query params: `status`, `game`, `format`, `page`, `limit`

### Create tournament
```http
POST /api/v1/projects/{projectId}/tournaments
```

```json
{
  "name": "Summer Championship 2026",
  "format": "single_elimination",
  "game": "cs2",
  "maxParticipants": 16,
  "registrationStart": "2026-06-20T00:00:00Z",
  "registrationEnd": "2026-06-23T23:59:00Z",
  "startDate": "2026-06-24T14:00:00Z",
  "prizePool": 5000,
  "currency": "USD"
}
```

### Get tournament
```http
GET /api/v1/projects/{projectId}/tournaments/{tournamentId}
```

### Publish tournament
```http
POST /api/v1/projects/{projectId}/tournaments/{tournamentId}/publish
```

### Generate bracket
```http
POST /api/v1/projects/{projectId}/tournaments/{tournamentId}/bracket/generate
```

### Get bracket
```http
GET /api/v1/projects/{projectId}/tournaments/{tournamentId}/bracket
```

### Get standings
```http
GET /api/v1/projects/{projectId}/tournaments/{tournamentId}/standings
```