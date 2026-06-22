# Developer

Base URL: `https://api.tournamentsuite.com/api/v1`

## Developer Dashboard (JWT, read-only)

| Method | Path | Description |
|--------|------|-------------|
| GET | `/developer/dashboard/stats` | `{ activeApiKeyCount, requestsLast24h, activeWebhookCount, errorRate }` |
| GET | `/developer/api-keys` | Masked list of your API keys (across all projects) |
| GET | `/developer/webhooks` | Webhook subscriptions list |
| GET | `/developer/webhooks/:id/deliveries` | Delivery history for a webhook |
| GET | `/developer/sandbox` | Sandbox environment status |
| GET | `/developer/sandbox/analytics` | Sandbox request analytics |
| GET | `/developer/api-reference` | Static endpoint catalog grouped by category |
| GET | `/developer/error-scenarios?errorCode=` | Error code details. Codes: `AUTH_001`, `AUTH_002`, `RATE_001`, `VAL_001`, `NOT_FOUND` |
| POST | `/developer/generate-code` | Code snippet generator. Body: `{ method, path, language: 'typescript'|'python'|'php'|'curl' }` |

> **Note:** Write operations on `/developer/*` (POST api-keys, webhooks, sandbox) return `503`. Use the project-scoped endpoints below.

---

## Project-Scoped API Keys

Requires JWT + project `OWNER` or `ADMIN` role.

Base: `/projects/:projectId/developer/api-keys`

### ProjectApiKeyDto

```json
{
  "id": "uuid",
  "name": "Production Integration",
  "keyPrefix": "ts_live_k3xA",
  "scopes": ["tournaments:read", "matches:write", "webhooks:manage"],
  "createdAt": "2026-01-10T08:00:00Z",
  "lastUsedAt": "2026-06-22T11:30:00Z",
  "expiresAt": null
}
```

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/` | JWT | List API keys. `keyPrefix` only — secret never shown again |
| POST | `/` | JWT | Create key. Body: `{ name, scopes: string[], expiresInDays? }` — secret returned exactly once |
| DELETE | `/:keyId` | JWT | Delete key. Body: `{ reason? }` |
| POST | `/:keyId/rotate` | JWT | Rotate key → new secret returned once |

Create response (secret shown once):
```json
{
  "id": "uuid",
  "name": "Production Integration",
  "key": "ts_live_k3xAbcDefGhIjKlMnOpQrStUvWxYz",
  "keyPrefix": "ts_live_k3xA",
  "scopes": ["tournaments:read", "matches:write"],
  "createdAt": "2026-06-22T12:00:00Z"
}
```

---

## Project-Scoped Webhooks

Requires JWT + project `OWNER` or `ADMIN` role.

Base: `/projects/:projectId/developer/webhooks`

### ProjectWebhookDto

```json
{
  "id": "uuid",
  "name": "Tournament results handler",
  "url": "https://your-server.com/webhooks/ts",
  "events": ["tournament.completed", "match.started"],
  "description": "Handles all tournament and match completion events",
  "status": "active",
  "circuitBreakerState": "CLOSED",
  "consecutiveFailures": 0,
  "circuitBreakerOpenedAt": null,
  "createdAt": "2026-06-22T10:00:00Z"
}
```

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/` | JWT | List webhooks |
| POST | `/` | JWT | Create webhook. Body: `{ name, url, events: string[], description?, secret? }` |
| PUT | `/:webhookId` | JWT | Update webhook (all fields optional) |
| DELETE | `/:webhookId` | JWT | Delete webhook |
| POST | `/:webhookId/test` | JWT | Send test event. Body: `{ eventType? }` |

---

## Scopes Reference

| Scope | Description |
|---|---|
| `tournaments:read` | Read tournament data |
| `tournaments:write` | Create and manage tournaments |
| `matches:read` | Read match data |
| `matches:write` | Submit scores and manage matches |
| `participants:read` | Read participant data |
| `participants:write` | Register and manage participants |
| `webhooks:manage` | Create and manage webhook subscriptions |
| `users:read` | Read public user profiles |
| `cs2:read` | Read CS2 server and match data |
| `cs2:write` | Provision servers and manage CS2 matches |

---

## Rate Limits

| Header | Description |
|---|---|
| `X-RateLimit-Limit` | Requests allowed per minute |
| `X-RateLimit-Remaining` | Requests remaining this minute |
| `X-RateLimit-Reset` | Unix timestamp when the window resets |

| Tier | Requests/min |
|---|---|
| Standard | 100 |
| File Upload | 10 |
| Admin | 200 |

When a limit is exceeded the API returns `429 Too Many Requests`.

---

## Sandbox

The sandbox environment mirrors production but uses isolated data. Sandbox keys are prefixed `ts_sandbox_`.

```
https://api.tournamentsuite.com/api/v1/sandbox
```

Use your `ts_sandbox_` key; all writes are scoped to the sandbox project and never affect live data.