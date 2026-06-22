# CS2 API

Base path: `/api/v1/projects/{projectId}/cs2`

## Overview

The CS2 API provides fleet management for dedicated servers, automated match lifecycle control, and on-demand rental servers for players. Server provisioning is handled by the `cs2-manager` service; the `cs2-node-agent` daemon runs on each host.

## Server States

| State | Value |
|---|---|
| Idle | `idle` |
| Configuring | `configuring` |
| Live | `live` |
| Resetting | `resetting` |
| Error | `error` |

---

## Fleet Management

### List servers
```http
GET /api/v1/projects/{projectId}/cs2/servers
```

Query params: `status`, `region`, `page`, `limit`

**Response:**
```json
[
  {
    "id": "server-uuid",
    "hostname": "cs2-eu-west-01.gamertd.com",
    "region": "eu-west",
    "status": "idle",
    "ip": "185.1.2.3",
    "port": 27015,
    "tvPort": 27020,
    "version": "1.39.7.4",
    "lastHeartbeat": "2026-06-22T12:59:50Z"
  }
]
```

### Get server
```http
GET /api/v1/projects/{projectId}/cs2/servers/{serverId}
```

### Provision server
```http
POST /api/v1/projects/{projectId}/cs2/servers
```

```json
{
  "region": "eu-west",
  "map": "de_mirage",
  "gameMode": "competitive",
  "maxRounds": 24,
  "tickRate": 128,
  "password": "matchpassword",
  "rconPassword": "rconpassword"
}
```

### Deprovision server
```http
DELETE /api/v1/projects/{projectId}/cs2/servers/{serverId}
```

### Send RCON command
```http
POST /api/v1/projects/{projectId}/cs2/servers/{serverId}/rcon
```

```json
{
  "command": "mp_restartgame 3"
}
```

**Response:**
```json
{
  "output": "L 06/22/2026 - 13:00:00: World triggered \"Game_Commencing\""
}
```

---

## Match Automation

### Assign server to match
```http
POST /api/v1/projects/{projectId}/cs2/servers/{serverId}/assign
```

```json
{
  "matchId": "match-uuid",
  "map": "de_dust2",
  "team1": {
    "name": "Team Alpha",
    "playerSteamIds": ["76561198012345678", "76561198023456789"]
  },
  "team2": {
    "name": "Team Beta",
    "playerSteamIds": ["76561198034567890", "76561198045678901"]
  },
  "cfg": "esl5on5"
}
```

### Get match server status
```http
GET /api/v1/projects/{projectId}/cs2/matches/{matchId}/server
```

Returns live scoreboard, current map, and round number sourced from the plugin.

### Get demo download URL
```http
GET /api/v1/projects/{projectId}/cs2/matches/{matchId}/demo
```

**Response:**
```json
{
  "url": "https://cdn.example.com/demos/match-uuid.dem.gz",
  "expiresAt": "2026-06-29T12:00:00Z"
}
```

---

## Rental Servers

### List available rental slots
```http
GET /api/v1/projects/{projectId}/cs2/rentals/available
```

Query params: `region`, `map`, `duration`

### Create rental session
```http
POST /api/v1/projects/{projectId}/cs2/rentals
Authorization: Bearer <token>
```

```json
{
  "region": "na-east",
  "map": "de_inferno",
  "duration": 60,
  "password": "mypracticeserver",
  "tickRate": 128
}
```

**Response:**
```json
{
  "id": "rental-uuid",
  "ip": "192.0.2.10",
  "port": 27015,
  "password": "mypracticeserver",
  "connect": "connect 192.0.2.10:27015; password mypracticeserver",
  "expiresAt": "2026-06-22T14:00:00Z",
  "creditsUsed": 60
}
```

### Get rental session
```http
GET /api/v1/projects/{projectId}/cs2/rentals/{rentalId}
Authorization: Bearer <token>
```

### Extend rental session
```http
POST /api/v1/projects/{projectId}/cs2/rentals/{rentalId}/extend
Authorization: Bearer <token>
```

```json
{
  "additionalMinutes": 30
}
```

### End rental session
```http
DELETE /api/v1/projects/{projectId}/cs2/rentals/{rentalId}
Authorization: Bearer <token>
```