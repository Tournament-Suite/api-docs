# Webhooks API

Base path: `/api/v1/projects/{projectId}/webhooks`

## Subscribe to events

```http
POST /api/v1/projects/{projectId}/webhooks
Authorization: Bearer <token>

{
  "url": "https://your-server.com/webhooks/ts",
  "events": ["tournament.completed", "match.started", "participant.registered"],
  "secret": "your-signing-secret",
  "description": "My tournament results handler"
}
```

**Response:**
```json
{
  "id": "webhook-uuid",
  "url": "https://your-server.com/webhooks/ts",
  "events": ["tournament.completed", "match.started", "participant.registered"],
  "status": "active",
  "createdAt": "2026-06-24T10:00:00Z"
}
```

## List subscriptions

```http
GET /api/v1/projects/{projectId}/webhooks
```

## Update subscription

```http
PATCH /api/v1/projects/{projectId}/webhooks/{webhookId}
```

## Delete subscription

```http
DELETE /api/v1/projects/{projectId}/webhooks/{webhookId}
```

## Delivery history

```http
GET /api/v1/projects/{projectId}/webhooks/{webhookId}/deliveries
```

## Redeliver event

```http
POST /api/v1/projects/{projectId}/webhooks/{webhookId}/deliveries/{deliveryId}/redeliver
```

## Test endpoint

```http
POST /api/v1/projects/{projectId}/webhooks/{webhookId}/test
```

Sends a `tournament.created` test payload to your endpoint.

---

See [WEBHOOK_EVENTS.md](../WEBHOOK_EVENTS.md) for the full event catalog and payload shapes.

See [webhook-examples](https://github.com/Tournament-Suite/webhook-examples) for copy-paste handlers.