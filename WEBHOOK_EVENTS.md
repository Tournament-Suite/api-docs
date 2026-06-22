# Webhook Events

Tournament Suite delivers webhook events for all major platform actions. Subscribe to any combination of events from the Developer Portal or via the API.

## Setup

```http
POST /api/v1/webhooks
Authorization: Bearer <token>

{
  "url": "https://your-server.com/webhooks/ts",
  "events": ["tournament.completed", "match.started"],
  "secret": "your-signing-secret"
}
```

## Delivery Envelope

Every webhook POST body is wrapped in this envelope:

```json
{
  "event": "tournament.completed",
  "timestamp": "2026-06-22T10:00:00Z",
  "deliveryId": "uuid",
  "subscriptionId": "uuid",
  "aggregateId": "tournament-uuid",
  "aggregateType": "tournament",
  "attemptNumber": 1,
  "data": { "..." }
}
```

## Signature Verification

Every webhook request includes two headers:

| Header | Value |
|---|---|
| `X-TS-Timestamp` | Unix timestamp (seconds) of delivery |
| `X-TS-Signature` | `sha256=<HMAC-SHA256 hex>` |

**Signature payload:** `timestamp.rawBody`

**Verification (Node.js):**
```ts
import crypto from 'crypto';

function verifySignature(
  rawBody: string,
  timestamp: string,
  signature: string,
  secret: string
): boolean {
  const payload = `${timestamp}.${rawBody}`;
  const expected = 'sha256=' + crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex');
  return crypto.timingSafeEqual(Buffer.from(signature), Buffer.from(expected));
}
```

**Replay protection:** Reject events where `Date.now()/1000 - timestamp > 300` (5 minutes).

→ Full examples in every language: [webhook-examples](https://github.com/Tournament-Suite/webhook-examples)

## Retry & Circuit Breaker

| State | Behavior |
|---|---|
| `CLOSED` | Normal delivery |
| `OPEN` | Endpoint failing — delivery paused, retried after backoff |
| `HALF_OPEN` | Test delivery sent; success → CLOSED, failure → OPEN |

Circuit breaker fields on the subscription object:

| Field | Type | Description |
|---|---|---|
| `circuitBreakerState` | `CLOSED` \| `OPEN` \| `HALF_OPEN` | Current state |
| `consecutiveFailures` | number | Failures since last success |
| `circuitBreakerOpenedAt` | ISO 8601 or null | When circuit opened |

Retries: exponential backoff, up to 72 hours. Max 10 attempts per event.

---

## Event Reference (32 event types)

### Tournament Events

#### `tournament.created`
Fired when a new tournament is created.

```json
{
  "event": "tournament.created",
  "timestamp": "2026-06-24T10:00:00Z",
  "data": {
    "tournamentId": "uuid",
    "projectId": "uuid",
    "name": "Summer Championship",
    "format": "single_elimination",
    "game": "cs2",
    "status": "draft"
  }
}
```

#### `tournament.published`
Fired when a tournament is published and registration opens.

#### `tournament.started`
Fired when the tournament bracket begins (first match goes live).

#### `tournament.completed`
Fired when the final match is reported and a winner is determined.

```json
{
  "event": "tournament.completed",
  "timestamp": "2026-06-24T18:00:00Z",
  "data": {
    "tournamentId": "uuid",
    "projectId": "uuid",
    "name": "Summer Championship",
    "winnerId": "participant-uuid",
    "winnerName": "Team Nexus",
    "prizePool": 5000,
    "currency": "USD"
  }
}
```

#### `tournament.cancelled`
Fired when a tournament is cancelled before completion.

#### `tournament.updated`
Fired when tournament metadata is edited (name, dates, format).

---

### Match Events

#### `match.created`
Fired when a match is generated (bracket seeding).

#### `match.started`
Fired when a match lobby opens and players are expected.

```json
{
  "event": "match.started",
  "timestamp": "2026-06-24T14:00:00Z",
  "data": {
    "matchId": "uuid",
    "tournamentId": "uuid",
    "round": 3,
    "participants": [
      { "id": "uuid", "name": "Team Nexus" },
      { "id": "uuid", "name": "Team Apex" }
    ],
    "scheduledAt": "2026-06-24T14:00:00Z"
  }
}
```

#### `match.completed`
Fired when a match result is submitted and confirmed.

```json
{
  "event": "match.completed",
  "timestamp": "2026-06-24T15:30:00Z",
  "data": {
    "matchId": "uuid",
    "tournamentId": "uuid",
    "winnerId": "participant-uuid",
    "winnerName": "Team Nexus",
    "score": { "Team Nexus": 2, "Team Apex": 0 }
  }
}
```

#### `match.disputed`
Fired when a participant raises a dispute on a match result.

#### `match.rescheduled`
Fired when a match is moved to a new scheduled time.

#### `match.forfeited`
Fired when a participant forfeits.

---

### Participant Events

#### `participant.registered`
Fired when a player or team submits a registration.

```json
{
  "event": "participant.registered",
  "timestamp": "2026-06-24T09:00:00Z",
  "data": {
    "participantId": "uuid",
    "tournamentId": "uuid",
    "userId": "uuid",
    "teamName": "Team Nexus",
    "registeredAt": "2026-06-24T09:00:00Z"
  }
}
```

#### `participant.approved`
Fired when registration is approved by the organizer.

#### `participant.rejected`
Fired when registration is rejected.

#### `participant.checked_in`
Fired when a participant completes check-in (online or on-site QR scan).

#### `participant.eliminated`
Fired when a participant is eliminated from the bracket.

#### `participant.disqualified`
Fired when a participant is disqualified (anti-cheat, code of conduct, etc.).

---

### Registration Events

#### `registration.opened`
Fired when registration opens for a tournament.

#### `registration.closed`
Fired when registration closes (deadline reached or manually closed).

#### `registration.full`
Fired when the participant cap is reached.

---

### Payment Events

#### `payment.received`
Fired when an entry fee or purchase is completed.

#### `payout.processed`
Fired when a prize payout is sent to a player wallet.

#### `payout.failed`
Fired when a payout attempt fails.

---

### Broadcast Events

#### `broadcast.started`
Fired when a live broadcast begins in the Broadcast Center.

#### `broadcast.ended`
Fired when a broadcast session ends.

---

### Circuit Events

#### `circuit.created`
Fired when a new circuit (multi-tournament series) is created.

#### `circuit.season.started`
Fired when a circuit season begins.

#### `circuit.season.completed`
Fired when a circuit season ends and final standings are locked.

---

### User Events

#### `user.created`
Fired when a new user account is created on the platform.

#### `user.verified`
Fired when identity verification is completed.

#### `user.suspended`
Fired when a user account is suspended.