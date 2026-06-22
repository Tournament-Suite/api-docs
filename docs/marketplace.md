# Marketplace

Base URL: `https://api.tournamentsuite.com/api/v1`

The marketplace allows players to buy and sell in-game items, tournament tickets, and collectibles.

---

## Public Browsing (no auth)

| Method | Path | Description |
|--------|------|-------------|
| GET | `/marketplace/items` | Browse all items. Filters: `?category` `?game` `?page` `?limit` |
| GET | `/marketplace/items/featured` | Featured/promoted items |
| GET | `/marketplace/listings` | Active listings. Filters: `?minPrice` `?maxPrice` `?sort` |
| GET | `/marketplace/listings/featured` | Featured listings |
| GET | `/marketplace/listings/recent` | Most recently posted listings |
| GET | `/marketplace/stats` | Aggregate stats (total listings, volume, active sellers) |

---

## Listings (JWT required)

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/marketplace/listings` | JWT | Create listing. Body: `{ itemId, price, currency, quantity? }` |
| GET | `/marketplace/listings/my` | JWT | Your active and past listings |
| GET | `/marketplace/listings/watchlist` | JWT | Your watchlisted listings |
| POST | `/marketplace/listings/:id/favorite` | JWT | Add to watchlist |
| DELETE | `/marketplace/listings/:id/favorite` | JWT | Remove from watchlist |
| GET | `/marketplace/listings/:id/similar` | Public | Similar listings by item type and price range |

---

## Transactions

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/marketplace/listings/:id/purchase` | JWT | Instant purchase at listed price |
| POST | `/marketplace/listings/:id/bids` | JWT | Place bid. Body: `{ amount, currency }` |
| POST | `/marketplace/listings/:id/offers` | JWT | Make offer. Body: `{ amount, currency, message? }` |

---

## History

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/marketplace/listings/transactions/my/purchases` | JWT | Your purchase history |
| GET | `/marketplace/listings/transactions/my/sales` | JWT | Your sales history |
| GET | `/marketplace/listings/transactions/my/stats` | JWT | Your marketplace stats (volume, avg sale price, etc.) |

---

## Reviews

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | `/marketplace/listings/transactions/:transactionId/reviews` | JWT | Leave review after transaction |
| GET | `/marketplace/sellers/:userId/reviews` | Public | Seller's review list |
| GET | `/marketplace/sellers/:userId/rating` | Public | Seller aggregate rating and stats |

---

## Billing

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/billing/plans` | Public | Available subscription plans |
| POST | `/billing/checkout` | JWT | Start checkout session. Body: `{ planId, successUrl, cancelUrl }` |
| GET | `/billing/subscription` | JWT | Current subscription status |
| GET | `/billing/invoices` | JWT | Invoice history |