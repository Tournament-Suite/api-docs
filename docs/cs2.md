# CS2

Base URL: `https://api.tournamentsuite.com/api/v1`

The CS2 API provides fleet management for dedicated servers, automated match lifecycle control, and on-demand rental servers. Server provisioning is handled by `cs2-manager`; the `cs2-node-agent` daemon runs on each host.

## Server States

| State | Value |
|---|---|
| Idle | `idle` |
| Configuring | `configuring` |
| Live | `live` |
| Resetting | `resetting` |
| Error | `error` |

---

## Public Endpoints (no auth)

| Method | Path | Description |
|--------|------|-------------|
| GET | `/public/cs2/servers?domain=` | List servers by tenant domain |
| GET | `/public/cs2/servers/by-project/:projectId` | List servers by project ID |
| GET | `/cs2/matches/:matchId/stats` | Per-player stats. Query: `?mapIndex=` |
| GET | `/cs2/matches/:matchId/demos` | Demo files for a match |
| GET | `/cs2/matches/:matchId/lobby` | Live lobby phase and veto state (JWT required) |

Server response example:
```json
{
  "id": "server-uuid",
  "hostname": "cs2-eu-west-01.tournamentsuite.com",
  "region": "eu-west",
  "status": "idle",
  "ip": "185.1.2.3",
  "port": 27015,
  "tvPort": 27020,
  "version": "1.39.7.4",
  "lastHeartbeat": "2026-06-22T12:59:50Z"
}
```

---

## Project-Scoped Server Management

Requires JWT + CS2 entitlement. Base: `/projects/:projectId/cs2/servers`

### CRUD & Lifecycle

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/` | JWT | Provision server |
| GET | `/` | JWT | List servers. Filters: `?status` `?region` `?page` `?limit` |
| GET | `/:id` | JWT | Get server detail |
| PATCH | `/:id` | JWT | Update server config |
| DELETE | `/:id` | JWT | Deprovision server |
| POST | `/:id/start` | JWT | Start server |
| POST | `/:id/stop` | JWT | Stop server |
| POST | `/:id/restart` | JWT | Restart server |
| POST | `/:id/kill` | JWT | Force-kill process |
| POST | `/:id/reinstall` | JWT | Reinstall server software |

### Operations

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/:id/rcon` | JWT | Send RCON command. Body: `{ command: string }` |
| POST | `/:id/console/token` | JWT | Get short-lived console WebSocket token |
| GET | `/:id/files` | JWT | List files on server |
| GET | `/:id/files/read` | JWT | Read a file. Query: `?path=` |
| PUT | `/:id/files/write` | JWT | Write a file. Body: `{ path, content }` |
| POST | `/:id/match` | JWT | Assign match to server |
| GET | `/:id/demos` | JWT | List demos recorded on this server |
| GET | `/:id/connect` | JWT | Get connect info (returns `ip:port` and `steam://` URL) |

---

## Rentals

Requires JWT + CS2 entitlement. Base: `/projects/:projectId/cs2/rentals`

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/availability` | JWT | Available rental slots. Filters: `?region` `?map` `?duration` |
| GET | `/` | JWT | List active/past rentals |
| GET | `/:id` | JWT | Get rental session |
| POST | `/` | JWT | Start rental. Body: `{ region, map, duration, password?, tickRate? }` |
| POST | `/:id/stop` | JWT | End rental early |
| POST | `/:id/extend` | JWT | Extend duration. Body: `{ additionalMinutes }` |
| POST | `/:id/rcon` | JWT | Send RCON to rental server |
| GET | `/:id/demos` | JWT | Demos recorded during rental |
| GET | `/:id/credits` | JWT | Credit usage for this rental |
| POST | `/:id/credits` | JWT | Add credits to rental |

Rental start response:
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

---

## API Keys

Requires JWT + CS2 entitlement.

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/projects/:projectId/cs2/api-keys` | JWT | List API keys (masked — full key never shown again) |
| POST | `/projects/:projectId/cs2/api-keys` | JWT | Create key — raw secret returned once |
| DELETE | `/projects/:projectId/cs2/api-keys/:id` | JWT | Revoke key |

---

## Plugin Webhooks (token-in-URL, internal)

Used by the `cs2-plugin-match` CounterStrikeSharp plugin. Requests are HMAC-SHA256 verified.

| Method | Path | Description |
|--------|------|-------------|
| POST | `/match-plugins/tournamentsuite-match/webhooks/:webhookToken/:event` | Plugin event callback |
| GET | `/match-plugins/tournamentsuite-match/configs/:serverId/:matchId.json` | Plugin fetches match config |

---

## Companion (token-in-URL, internal)

Used by the `cs2-plugin-companion` plugin for player linking and demo uploads.

| Method | Path | Description |
|--------|------|-------------|
| POST | `/companion/heartbeat/:webhookToken` | Plugin heartbeat |
| GET | `/companion/admission/:webhookToken?steamId=` | Check if Steam ID is admitted to server |
| POST | `/companion/link/:webhookToken` | Generate `!link` OTP |
| GET | `/companion/link/:webhookToken/:steamId` | Verify link OTP |
| POST | `/companion/demo-uploaded/:webhookToken` | Notify of uploaded demo |
| POST | `/companion/demo-upload-url/:webhookToken` | Request pre-signed demo upload URL |