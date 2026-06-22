# Participants

Base URL: `https://api.tournamentsuite.com/api/v1`

## Participant Object

```json
{
  "id": "uuid",
  "tournamentId": "uuid",
  "name": "Team Nexus",
  "type": "team",
  "status": "accepted",
  "seed": 3,
  "currentPosition": null,
  "wins": 2,
  "losses": 0,
  "points": 150,
  "userId": "uuid"
}
```

### Type Values

| Type | Value |
|---|---|
| Individual player | `individual` |
| Team | `team` |

### Status Values

| Status | Value |
|---|---|
| Pending approval | `pending` |
| Accepted | `accepted` |
| Refused | `refused` |
| Cancelled | `cancelled` |
| Waitlisted | `waitlisted` |
| Checked in | `checked_in` |
| Eliminated | `eliminated` |
| Disqualified | `disqualified` |

---

## Participant Endpoints

Base: `/tournaments/:tournamentId/participants`

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/` | JWT | Register participant |
| DELETE | `/:id` | JWT | Unregister (self or organizer) |
| GET | `/` | Public | List all participants |
| GET | `/checkedin/list` | Organizer | List checked-in participants |
| GET | `/waitlist/list` | Organizer | List waitlisted participants |
| POST | `/bulk-checkin` | Organizer | Bulk check-in. Body: `{ participantIds: string[] }` |
| GET | `/:id` | Public | Get single participant |
| PUT | `/:id` | Organizer | Update participant fields (seed, etc.) |
| DELETE | `/:id` | Organizer | Delete participant record |
| POST | `/:id/checkin` | JWT | Check in a participant |
| POST | `/:id/checkout` | JWT | Reverse check-in |
| POST | `/:id/disqualify` | Organizer | Disqualify. Body: `{ reason, notes? }` |
| POST | `/:id/reinstate` | Organizer | Reinstate a disqualified participant |
| GET | `/:id/statistics` | Public | Per-participant tournament statistics |
| GET | `/:id/matches` | Public | Matches played by this participant |
| POST | `/:id/promote` | Organizer | Promote from waitlist to accepted |
| POST | `/:id/substitute` | Organizer | Substitute a player in a team. Body: `{ outUserId, inUserId }` |
| PUT | `/:id/rating` | Organizer | Override rating for seeding purposes |

---

## Registration Workflow

Base: `/tournaments/:tournamentId/registration`

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/settings` | Organizer | Get registration settings |
| POST | `/:id/approve` | Organizer | Approve a registration |
| POST | `/:id/reject` | Organizer | Reject a registration. Body: `{ reason }` |
| PATCH | `/bulk-update` | Organizer | Bulk update statuses. Body: `{ ids: string[], status }` |
| GET | `/waitlist` | Organizer | Get full waitlist |
| POST | `/waitlist/promote` | Organizer | Promote waitlisted entry. Body: `{ participantId }` |
| GET | `/status` | JWT | Get caller's own registration status |
| GET | `/query` | Organizer | Query registrations with filters |

### Registration Status Flow

```
pending → accepted → checked_in
       ↘ refused
       ↘ cancelled (self)
       ↘ waitlisted (cap reached)
```

---

## Check-in

Base: `/tournaments/:tournamentId/checkin`

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/enable` | Organizer | Open the check-in window |
| POST | `/disable` | Organizer | Close the check-in window |
| GET | `/status` | Public | Check-in window status (open/closed, times) |
| GET | `/participant/:id/status` | JWT | Check-in status for a specific participant |
| POST | `/bulk-enable` | Organizer | Enable check-in for multiple participants |
| GET | `/statistics` | Organizer | Check-in stats: total, checked-in count, rate |
| POST | `/remind` | Organizer | Send check-in reminder notification to unchecked participants |
| GET | `/settings` | Organizer | Check-in configuration (window, QR mode, etc.) |