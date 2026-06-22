# Tournament Suite API Docs

Official API documentation for the [Tournament Suite](https://tournamentsuite.com) platform.

## Base URLs

| Environment | URL |
|---|---|
| Production | `https://api.tournamentsuite.com/api/v1` |
| Staging | `https://api.tournamentsuite.com/api/v1` |
| Sandbox | `https://api.tournamentsuite.com/api/v1/sandbox` |

## Authentication

Tournament Suite uses **Keycloak RS256 JWT** for authentication.

```http
Authorization: Bearer <your-jwt-token>
Content-Type: application/json
```

Obtain a token via:
- **PKCE flow** — for organizer and player web apps
- **API Key** — for server-to-server integrations (generate from the Developer Portal)
- **Sandbox** — sandbox tokens are available at `GET /developer/sandbox`

## SDK

```bash
npm install @tournamentsuite/sdk
```

```ts
import { TournamentSuiteClient } from '@tournamentsuite/sdk';

const client = new TournamentSuiteClient({ apiKey: 'YOUR_API_KEY' });
const tournaments = await client.tournaments.list({ projectId: 'YOUR_PROJECT_ID' });
```

→ [sdk-js repo](https://github.com/Tournament-Suite/sdk-js)

## API Reference

| Domain | Description |
|---|---|
| [Tournaments](docs/tournaments.md) | Create, manage, and track tournaments |
| [Matches](docs/matches.md) | Match lifecycle, scoring, bracket, seeding |
| [Participants](docs/participants.md) | Registration, check-in, elimination |
| [Circuits](docs/circuits.md) | Multi-event circuits and season standings |
| [Users](docs/users.md) | Player profiles, stats, social |
| [Webhooks](docs/webhooks.md) | Subscribe to platform events |
| [CS2](docs/cs2.md) | CS2 server fleet, match automation |
| [Developer](docs/developer.md) | API keys, sandbox, rate limits |
| [Matchmaking](docs/matchmaking.md) | Skill-based queue system |
| [Marketplace](docs/marketplace.md) | Item listings, purchases, reviews |

## Webhook Events

→ [WEBHOOK_EVENTS.md](WEBHOOK_EVENTS.md) — all 32 event types with payload shapes and HMAC verification

→ [webhook-examples](https://github.com/Tournament-Suite/webhook-examples) — copy-paste handlers in Node, Python, Go, PHP

## OpenAPI Spec

- [`openapi.json`](openapi.json) — machine-readable spec (OpenAPI 3.0)
- [`openapi.yaml`](openapi.yaml) — YAML equivalent
- [`postman_collection.json`](postman_collection.json) — import into Postman

Interactive docs: [api.tournamentsuite.com/api/docs](https://api.tournamentsuite.com/api/docs)

## Rate Limits

| Tier | Requests/min |
|---|---|
| Standard | 100 |
| File Upload | 10 |
| Admin | 200 |

Rate limit headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`

## Versioning

The API is versioned via URL path (`/api/v1`). Breaking changes increment the version. Non-breaking additions are made within the current version.

## Support

- Developer docs: [tournamentsuite.com/developer](https://tournamentsuite.com/developer)
- Q&A: [GitHub Discussions](https://github.com/orgs/Tournament-Suite/discussions/categories/q-a)
- Email: support@tournamentsuite.com