# Developer API

Base path: `/api/v1/developer`

## Overview

The Developer API lets you manage API keys, explore the sandbox environment, inspect rate limit state, and manage webhook subscriptions programmatically. All endpoints require a valid Bearer token with the `developer` role.

---

## API Keys

### List API keys
```http
GET /api/v1/developer/keys
Authorization: Bearer <token>
```

**Response:**
```json
[
  {
    "id": "key-uuid",
    "name": "Production Integration",
    "prefix": "ts_live_k3xA",
    "scopes": ["tournaments:read", "matches:write", "webhooks:manage"],
    "createdAt": "2026-01-10T08:00:00Z",
    "lastUsedAt": "2026-06-22T11:30:00Z",
    "expiresAt": null
  }
]
```

### Create API key
```http
POST /api/v1/developer/keys
Authorization: Bearer <token>
```

```json
{
  "name": "Production Integration",
  "scopes": ["tournaments:read", "matches:write", "webhooks:manage"],
  "expiresAt": null
}
```

**Response:**
```json
{
  "id": "key-uuid",
  "name": "Production Integration",
  "key": "ts_live_k3xAbcDefGhIjKlMnOpQrStUvWxYz",
  "prefix": "ts_live_k3xA",
  "scopes": ["tournaments:read", "matches:write", "webhooks:manage"],
  "createdAt": "2026-06-22T12:00:00Z"
}
```

> The full key value is only returned once. Store it securely.

### Rotate API key
```http
POST /api/v1/developer/keys/{keyId}/rotate
Authorization: Bearer <token>
```

Issues a new secret for the key. The previous secret is invalidated immediately.

**Response:**
```json
{
  "id": "key-uuid",
  "key": "ts_live_newSecretValue",
  "rotatedAt": "2026-06-22T12:05:00Z"
}
```

### Delete API key
```http
DELETE /api/v1/developer/keys/{keyId}
Authorization: Bearer <token>
```

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

## Sandbox

The sandbox environment mirrors production but uses isolated data. Sandbox keys are prefixed `ts_sandbox_`.

### Get sandbox status
```http
GET /api/v1/developer/sandbox
Authorization: Bearer <token>
```

### Reset sandbox data
```http
POST /api/v1/developer/sandbox/reset
Authorization: Bearer <token>
```

Resets all tournaments, matches, and participants created in your sandbox project. Irreversible.

### Sandbox base URL

```
https://api.gamertd.com/api/v1/
```

Use your `ts_sandbox_` key; all writes are scoped to the sandbox project and never affect live data.

---

## Rate Limits

All API keys are subject to per-minute and per-day request limits. Limits are returned in response headers:

| Header | Description |
|---|---|
| `X-RateLimit-Limit` | Requests allowed per minute |
| `X-RateLimit-Remaining` | Requests remaining this minute |
| `X-RateLimit-Reset` | Unix timestamp when the window resets |

When a limit is exceeded the API returns `429 Too Many Requests`.

### Get current rate limit state
```http
GET /api/v1/developer/rate-limits
Authorization: Bearer <token>
```

**Response:**
```json
{
  "perMinute": { "limit": 300, "remaining": 287, "resetsAt": "2026-06-22T12:01:00Z" },
  "perDay": { "limit": 50000, "remaining": 48123, "resetsAt": "2026-06-23T00:00:00Z" }
}
```

Default limits by plan:

| Plan | Per Minute | Per Day |
|---|---|---|
| Free | 60 | 5,000 |
| Pro | 300 | 50,000 |
| Enterprise | 1,000 | 500,000 |

---

## Webhook Subscriptions

Webhook subscriptions can also be managed via the Developer API for cross-project automation. See [webhooks.md](./webhooks.md) for the full event catalog and payload shapes.

### List all subscriptions (all projects)
```http
GET /api/v1/developer/webhooks
Authorization: Bearer <token>
```

### Test delivery
```http
POST /api/v1/developer/webhooks/{webhookId}/test
Authorization: Bearer <token>
```

Sends a synthetic `ping` event to verify endpoint reachability and signing.