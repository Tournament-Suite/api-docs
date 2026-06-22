# Matches

Base URL: `https://api.tournamentsuite.com/api/v1`

## Public Cross-Tournament Matches

These endpoints return matches from all public tournaments without requiring a tournament context.

### Endpoints

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/matches/live` | Public | Live matches. Filters: `?disciplineId` `?disciplineSlug` |
| GET | `/matches/upcoming` | Public | Upcoming matches. Filters: `?timeRange` `?disciplineId` `?disciplineSlug` |
| GET | `/matches/recent` | Public | Recently completed. Filters: `?timeRange` `?disciplineId` |
| GET | `/matches/:id` | Public | Single match (public tournaments only) |
| GET | `/matches/:id/games` | Public | Individual games in a series (Bo3, Bo5, etc.) |
| GET | `/matches/:id/stats` | Public | Aggregated per-player statistics |
| GET | `/matches/:id/engagement` | Public | Spectator count, predictions, polls, stream availability |
| GET | `/matches/:id/engagement/me` | Optional JWT | Authenticated viewer's own prediction and engagement state |
| POST | `/matches/:id/polls/active/vote` | JWT | Vote in the active live poll. Body: `{ optionId: string }` |
| POST | `/matches/:id/rewards/claim` | JWT | Claim watch reward. Requires watch-time threshold. Body: `{ campaignId?: string }` |
| GET | `/matches/tournaments/:tournamentId/engagement` | Public | Tournament-level spectator rollup |

### Match Object

```json
{
  "id": "uuid",
  "tournamentId": "uuid",
  "stageId": "uuid",
  "number": 1,
  "status": "ongoing",
  "format": "bo3",
  "scheduledAt": "2026-06-22T14:00:00Z",
  "playedAt": "2026-06-22T14:05:00Z",
  "opponents": [
    { "number": 1, "participantId": "uuid", "score": 2, "result": "win", "winner": true },
    { "number": 2, "participantId": "uuid", "score": 1, "result": "loss", "winner": false }
  ],
  "winnerId": "uuid",
  "bestOf": 3
}
```

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

---

## Tournament-Scoped Matches

All endpoints below are nested under `/tournaments/:tournamentId/matches`.
Write operations require `tournament:organizer`, `tournament:admin`, or `admin` role.

### CRUD

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/tournaments/:tournamentId/matches` | Organizer | Create match. Body: `CreateMatchDto` |
| GET | `/tournaments/:tournamentId/matches` | Public | List matches with filters |
| GET | `/tournaments/:tournamentId/matches/:matchId` | Public | Single match details |
| PATCH | `/tournaments/:tournamentId/matches/:matchId` | Organizer | Update match fields |
| DELETE | `/tournaments/:tournamentId/matches/:matchId` | Organizer | Delete (not in-progress) |

### Lifecycle

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/tournaments/:tournamentId/matches/:matchId/start` | Organizer | Start → `in_progress`, records `startedAt` |
| POST | `/tournaments/:tournamentId/matches/:matchId/complete` | Organizer | Complete with scores and winner |
| POST | `/tournaments/:tournamentId/matches/:matchId/forfeit` | Organizer | Forfeit. Body: `{ winnerId, reason? }` |
| POST | `/tournaments/:tournamentId/matches/:matchId/reset` | Organizer | Reset to pending |
| PATCH | `/tournaments/:tournamentId/matches/:matchId/reschedule` | Organizer | Reschedule. Body: `{ scheduledAt }` |

### Scoring

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| PATCH | `/tournaments/:tournamentId/matches/:matchId/score` | Organizer | Update live score |
| POST | `/tournaments/:tournamentId/matches/:matchId/games` | Organizer | Add a game result to a series |
| PATCH | `/tournaments/:tournamentId/matches/:matchId/games/:gameId` | Organizer | Update individual game |
| POST | `/tournaments/:tournamentId/matches/:matchId/result` | Organizer | Submit final result |

### Disputes

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/tournaments/:tournamentId/matches/:matchId/disputes` | JWT (any) | Open a dispute for a match |

### Scheduling

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/tournaments/:tournamentId/matches/auto-schedule` | Organizer | Auto-schedule all pending matches |
| POST | `/tournaments/:tournamentId/matches/bulk-schedule` | Organizer | Bulk schedule multiple matches |
| GET | `/tournaments/:tournamentId/matches/scheduling-conflicts` | Organizer | Detect scheduling conflicts |
| GET | `/tournaments/:tournamentId/matches/available-slots` | Organizer | Available time slots |

---

## Bracket

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/tournaments/:tournamentId/bracket` | JWT | Bracket structure |
| GET | `/tournaments/:tournamentId/bracket/visualization` | JWT | Visualization-ready bracket data |
| POST | `/tournaments/:tournamentId/bracket/generate` | Organizer | Generate bracket |
| PUT | `/tournaments/:tournamentId/bracket/update` | Organizer | Update bracket |
| POST | `/tournaments/:tournamentId/bracket/seed` | Organizer | Apply seeding |
| POST | `/tournaments/:tournamentId/bracket/swap` | Organizer | Swap two seeds |

---

## Seeding

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/tournaments/:tournamentId/seeding` | JWT | Get current seeding |
| PUT | `/tournaments/:tournamentId/seeding` | Organizer | Update seeds |
| POST | `/tournaments/:tournamentId/seeding/generate` | Organizer | Auto-generate seeds |
| POST | `/tournaments/:tournamentId/seeding/auto-seed` | Organizer | Apply auto-seeding |
| GET | `/tournaments/:tournamentId/seeding/preview` | Organizer | Preview seeding result |

---

## Standings

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/tournaments/:tournamentId/standings` | Public | Current standings |
| POST | `/tournaments/:tournamentId/standings/generate` | Organizer | Regenerate standings |
| POST | `/tournaments/:tournamentId/standings/finalize` | Organizer | Finalize standings |
| GET | `/tournaments/:tournamentId/stages/:stageId/standings` | Public | Stage-level standings |
