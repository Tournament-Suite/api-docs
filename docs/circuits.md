# Circuits

Base URL: `https://api.tournamentsuite.com/api/v1`

Circuits group multiple tournaments into a season-long competitive series. Points accumulate across events; top qualifiers advance to the circuit final.

## Circuit States

| State | Value |
|---|---|
| Draft | `draft` |
| Active | `active` |
| Completed | `completed` |
| Archived | `archived` |

---

## Circuit CRUD

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/circuits` | JWT | Create a circuit |
| GET | `/circuits` | JWT | List circuits |
| GET | `/circuits/:id` | JWT | Get single circuit |
| PUT | `/circuits/:id` | JWT | Update circuit |
| DELETE | `/circuits/:id` | JWT | Delete circuit (draft only) |

---

## Discovery (Public)

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/circuits/popular` | Public | Most-followed active circuits |
| GET | `/circuits/upcoming` | Public | Circuits starting soon |
| GET | `/circuits/public/active` | Public | All currently active circuits |
| GET | `/circuits/public/:id` | Public | Public circuit detail |

---

## Lifecycle

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| PUT | `/circuits/:id/status` | JWT | Set circuit status |
| POST | `/circuits/:id/publish` | JWT | Publish circuit |
| POST | `/circuits/:id/start` | JWT | Start circuit (activates season) |
| POST | `/circuits/:id/complete` | JWT | Complete circuit and lock standings |

---

## Analytics

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/circuits/:id/statistics` | Public | Aggregate statistics (tournaments, participants, prize pools) |
| GET | `/circuits/:id/analytics` | JWT | Detailed analytics (retention, region breakdown, engagement) |
| GET | `/circuits/:id/sponsors` | Public | Sponsor list for the circuit |
| GET | `/circuits/:id/tournaments` | Public | Tournaments included in the circuit |

---

## Standings

### Circuit Standings

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/circuits/:circuitId/standings` | JWT | Full standings. Filters: `?type` `?region` `?page` `?limit` |
| GET | `/standings/public` | Public | Public-facing circuit standings |
| GET | `/standings/public/top/:count` | Public | Top N ranked entries |
| GET | `/standings/search` | Public | Search standings. Query: `?query=` |
| GET | `/standings/statistics` | JWT | Standings aggregate statistics |
| GET | `/standings/distribution` | JWT | Points distribution across ranks |
| GET | `/standings/participant/:participantId` | JWT | Single participant's standing |
| GET | `/standings/participant/:participantId/history` | JWT | Participant standings history across seasons |

---

## Seasons

Base: `/circuits/:circuitId/seasons`

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/` | JWT | Create a season |
| GET | `/` | JWT | List seasons for this circuit |
| GET | `/:id` | JWT | Get season detail |
| PUT | `/:id` | JWT | Update season |
| PUT | `/:id/status` | JWT | Change season status |
| DELETE | `/:id` | JWT | Delete season (draft only) |

---

## Rankings (v2)

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/circuit/v2/rankings` | JWT | Paginated rankings (Range header). Filters: `ranking_ids`, `circuit_ids`, `discipline`, `entity_type`, `season_ids` |
| GET | `/circuit/v2/rankings/:id` | JWT | Single ranking entry |
| GET | `/circuit/v2/rankings/circuit/:circuitId/categorized` | JWT | Rankings grouped by category |

Range header pagination: `Range: rankings=0-49` → `Content-Range: rankings 0-49/250`

---

## Regions

Base: `/circuits/:circuitId/regions`

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/` | JWT | Create region |
| GET | `/` | JWT | List regions |
| GET | `/:id` | JWT | Get region |
| PUT | `/:id` | JWT | Update region |
| DELETE | `/:id` | JWT | Delete region |

---

## Tiers

Base: `/circuits/:circuitId/tiers`

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/` | JWT | Create tier |
| GET | `/` | JWT | List tiers |
| GET | `/:id` | JWT | Get tier |
| PUT | `/:id` | JWT | Update tier |
| DELETE | `/:id` | JWT | Delete tier |

---

## Circuit Participants

Base: `/circuits/:circuitId/participants`

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/register` | JWT | Register for circuit |
| GET | `/` | JWT | List participants |
| GET | `/:id` | JWT | Get participant |
| GET | `/:id/statistics` | JWT | Participant stats (points per event, wins, top placements) |
| GET | `/:id/history` | JWT | Event-by-event result history |
| PUT | `/:id` | Admin | Update participant data |
| DELETE | `/:id` | Admin | Remove participant |
| PUT | `/:id/admin/status` | Admin | Override participant status |

---

## Tournament Classification

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/circuit/v2/tournaments/:id/classify` | JWT | Get classification config for a tournament |
| POST | `/circuit/v2/tournaments/:id/classify` | JWT | Classify tournament into circuit |
| PATCH | `/circuit/v2/tournaments/:id/classify` | JWT | Update classification |
| DELETE | `/circuit/v2/tournaments/:id/classify` | JWT | Remove classification |
| GET | `/circuit/v2/tournaments/circuit/:circuitId/summary` | JWT | Summary of all classified tournaments in a circuit |