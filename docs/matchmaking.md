# Matchmaking

The matchmaking system pairs players into matches based on skill rating, queue preferences, and availability.

Base URL: `https://api.tournamentsuite.com/api/v1`

## Queue Endpoints

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/matchmaking/queues` | JWT | List all active queues |
| GET | `/matchmaking/queues/:id` | JWT | Queue details |
| GET | `/matchmaking/queues/:id/stats` | JWT | Queue statistics (player count, avg wait time) |
| POST | `/matchmaking/join` | JWT | Join a queue. Body: `{ queueId: string; teamId?: string }` |
| DELETE | `/matchmaking/leave` | JWT | Leave current queue |
| GET | `/matchmaking/status` | JWT | Current user's queue position and estimated wait |

## Queue Object

```json
{
  "id": "uuid",
  "name": "Ranked 5v5",
  "disciplineId": "uuid",
  "minPlayers": 10,
  "maxPlayers": 10,
  "estimatedWaitMs": 45000,
  "playersInQueue": 23,
  "status": "active"
}
```

## Flow

1. Call `POST /matchmaking/join` with the queue ID
2. Poll `GET /matchmaking/status` for your position
3. When a match is found, you receive a WebSocket event (`matchmaking.match_found`) with `matchId`
4. Call `GET /cs2/matches/:matchId/connect` to get the server connect URL
5. After the match, your rating is updated automatically