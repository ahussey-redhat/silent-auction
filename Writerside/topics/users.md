# Users

Users are identities managed in Keycloak; the backend maps the authenticated OIDC identity to a user record rather than handling registration or credentials itself. Membership of the admin group (configured via `ADMIN_GROUP_NAME` in the frontend) determines whether a user sees admin-only views and actions.

Two sets of endpoints exist: an admin surface for managing any user, and a `/me` surface scoped to the caller's own identity.

## Endpoints

| Method | Path | Access |
|---|---|---|
| `GET` / `POST` | `/api/v1/users` | Admin |
| `GET` | `/api/v1/users/{id}` | Admin |
| `GET` | `/api/v1/users/{id}/bids` | Admin |
| `GET` / `POST` | `/api/v1/me` | Bidder |
| `GET` | `/api/v1/me/bids` | Bidder |

In the frontend, user management is an admin-only screen; every other user only ever sees their own profile and bid history via the `/me` endpoints.