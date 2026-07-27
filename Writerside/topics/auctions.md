# Auctions

An auction is the top-level entity being bid on. Anyone can browse open auctions and see the current highest bid; creating, updating, and deleting auctions is restricted to admins.

In the frontend, auctions are browsed from the auction list and viewed individually at `/auctions/[id]`, which shows bid history and a place-bid form. The backend also pushes live updates over WebSocket (all auctions every 60s, highest bids every 10s) so open pages stay current without polling.

## Endpoints

| Method | Path | Access |
|---|---|---|
| `GET` | `/api/v1/auctions` | Public |
| `GET` | `/api/v1/auctions/{id}` | Public |
| `GET` | `/api/v1/auctions/{id}/bids/highest` | Public |
| `POST` | `/api/v1/auctions` | Admin |
| `PUT` / `DELETE` | `/api/v1/auctions/{id}` | Admin |
| `GET` | `/api/v1/auctions/{id}/bids` | Admin |

The OpenAPI spec is served by the backend itself at `/openapi` (Swagger UI at `/swagger-ui`) — see [`silent-auction-backend`](https://github.com/ahenshaw-work/silent-auction-backend) for how to run it locally.