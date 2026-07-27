# Bids

A bid places a monetary offer against an auction. Any authenticated user can submit a bid; viewing or removing individual bids outside of "highest bid" lookups is an admin operation.

The frontend enforces a minimum increment above the current highest bid (`BID_INCREMENT`, configured per environment) before submitting — see [Architecture](architecture.md) for how frontend configuration is injected at runtime.

## Endpoints

| Method | Path | Access |
|---|---|---|
| `POST` | `/api/v1/bids` | Authenticated |
| `GET` | `/api/v1/bids` | Admin |
| `GET` / `DELETE` | `/api/v1/bids/{id}` | Admin |

A bidder's own bid history is available without admin rights via `GET /api/v1/me/bids` — see [Users](users.md).