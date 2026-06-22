# Users

Base URL: `https://api.tournamentsuite.com/api/v1`

## User Object

```json
{
  "id": "uuid",
  "email": "player@example.com",
  "username": "player123",
  "displayName": "Player 123",
  "avatarUrl": "https://cdn.tournamentsuite.com/avatars/player123.png",
  "emailVerified": true,
  "role": "player",
  "createdAt": "2024-01-15T10:00:00Z"
}
```

---

## Profile

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/users/:id` | Public | Public profile |
| GET | `/users/me` | JWT | Current user's full profile |
| PATCH | `/users/me` | JWT | Update profile fields (displayName, bio, country, socialLinks) |
| GET | `/users/:id/stats/:game` | Public | Per-game stats for a user |

---

## Social

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/users/:id/follow` | JWT | Follow a user |
| DELETE | `/users/:id/follow` | JWT | Unfollow a user |
| GET | `/users/:id/followers` | Public | Followers list. Query: `?page` `?limit` |
| GET | `/users/:id/following` | Public | Following list. Query: `?page` `?limit` |
| POST | `/users/:id/block` | JWT | Block a user |
| DELETE | `/users/:id/block` | JWT | Unblock a user |
| GET | `/users/me/mutual-follows` | JWT | Users who follow you and you follow back |

---

## Leaderboard

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/users/leaderboard` | Public | Global leaderboard. Filters: `?game` `?region` `?page` `?limit` |
| GET | `/users/leaderboard/game/:gameId` | Public | Game-specific leaderboard |

---

## Career

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/users/me/career` | JWT | Career summary (earnings, tournament count, win rate) |
| GET | `/users/me/tournament-history` | JWT | Paginated tournament history with placements |
| GET | `/users/me/achievements` | JWT | Earned achievements and badges |

---

## Game Accounts

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/users/me/game-accounts` | JWT | List linked game accounts |
| POST | `/users/me/game-accounts` | JWT | Link account. Body: `{ platform: 'steam'|'riot'|'epic', accountId }` |
| DELETE | `/users/me/game-accounts/:platform` | JWT | Unlink a platform account |

Steam accounts are verified via the Steam OpenID flow (`/auth/steam`). Other platforms use the provider's OAuth or API key verification.

**Linked Account Object:**
```json
{
  "platform": "steam",
  "accountId": "76561198012345678",
  "username": "player123",
  "verifiedAt": "2026-01-10T08:00:00Z"
}
```

---

## Settings

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/users/me/settings` | JWT | Get user settings |
| PATCH | `/users/me/settings` | JWT | Update settings |

Settings body example:
```json
{
  "notifications": {
    "email": true,
    "matchReminders": true,
    "marketplaceOffers": false
  },
  "privacy": {
    "showEmail": false,
    "showEarnings": false,
    "profileVisibility": "public"
  }
}
```