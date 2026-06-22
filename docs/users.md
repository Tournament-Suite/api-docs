# Users API

Base path: `/api/v1/users`

## Endpoints

### Get current user
```http
GET /api/v1/users/me
Authorization: Bearer <token>
```

**Response:**
```json
{
  "id": "user-uuid",
  "username": "player123",
  "displayName": "Player 123",
  "email": "player@example.com",
  "avatar": "https://cdn.example.com/avatars/player123.png",
  "country": "US",
  "createdAt": "2024-01-15T10:00:00Z",
  "roles": ["player"]
}
```

### Get user profile (public)
```http
GET /api/v1/users/{userId}
```

### Update profile
```http
PATCH /api/v1/users/me
Authorization: Bearer <token>
```

```json
{
  "displayName": "Player 123",
  "bio": "Top-ranked CS2 player.",
  "country": "US",
  "socialLinks": {
    "twitter": "https://twitter.com/player123",
    "twitch": "https://twitch.tv/player123"
  }
}
```

### Upload avatar
```http
POST /api/v1/users/me/avatar
Authorization: Bearer <token>
Content-Type: multipart/form-data
```

Field: `file` (JPEG or PNG, max 2 MB)

---

## Game Accounts

Base path: `/api/v1/users/me/game-accounts`

### List linked game accounts
```http
GET /api/v1/users/me/game-accounts
Authorization: Bearer <token>
```

**Response:**
```json
[
  {
    "game": "cs2",
    "accountId": "76561198012345678",
    "username": "player123",
    "verifiedAt": "2026-01-10T08:00:00Z"
  }
]
```

### Link game account
```http
POST /api/v1/users/me/game-accounts
Authorization: Bearer <token>
```

```json
{
  "game": "cs2",
  "accountId": "76561198012345678"
}
```

Steam accounts are verified via the Steam OpenID flow (`/auth/steam`). Other games use the provider's OAuth or API key verification.

### Unlink game account
```http
DELETE /api/v1/users/me/game-accounts/{game}
Authorization: Bearer <token>
```

---

## Stats

### Get player stats
```http
GET /api/v1/users/{userId}/stats
```

Query params: `game`, `season`, `projectId`

**Response:**
```json
{
  "userId": "user-uuid",
  "game": "cs2",
  "stats": {
    "tournamentsPlayed": 12,
    "wins": 4,
    "top3": 7,
    "earnings": 2500,
    "currency": "USD",
    "rating": 1842
  }
}
```

### Get match history
```http
GET /api/v1/users/{userId}/matches
```

Query params: `game`, `tournamentId`, `page`, `limit`

---

## Social

### Follow user
```http
POST /api/v1/users/{userId}/follow
Authorization: Bearer <token>
```

### Unfollow user
```http
DELETE /api/v1/users/{userId}/follow
Authorization: Bearer <token>
```

### Get followers
```http
GET /api/v1/users/{userId}/followers
```

Query params: `page`, `limit`

### Get following
```http
GET /api/v1/users/{userId}/following
```

Query params: `page`, `limit`