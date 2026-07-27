# ADR-0001: Keycloak OIDC for Cross-Component Authentication

| Field | Value |
|---|---|
| **Status** | Accepted |

---

## Context

Silent Auction has three independent clients that all need to know who's calling and whether they're an admin: the web frontend, the CLI, and (indirectly, as the party enforcing it) the backend API itself. These clients don't share a runtime or a language, so any identity solution has to work as a standalone service they each integrate with independently, rather than shared in-process auth code.

The domain also has a simple but real authorization split — most read operations are public or "any authenticated user," while creating/editing auctions, viewing all bids, and managing users are admin-only (see [Auctions](auctions.md), [Bids](bids.md), [Users](users.md)).

## Decision

A single **Keycloak** realm is the identity provider for all three clients:

- **Frontend** authenticates end users via the OIDC **PKCE** flow (`keycloak-js`), refreshes the token on a 60-second interval, and derives `isAdmin` from membership of the realm group named by `ADMIN_GROUP_NAME`.
- **CLI** authenticates via `python-keycloak`, using a service account or direct grant rather than a browser redirect flow.
- **Backend** never issues or validates credentials itself — it's a pure OIDC resource server. Endpoints are annotated `@Authenticated`/`@RolesAllowed`, and Keycloak's policy enforcer is active in the `%prod` profile (disabled in dev, where Quarkus Dev Services stands up a throwaway Keycloak instance automatically).

Admin/bidder distinction is therefore enforced once, at the source of truth (Keycloak group membership), rather than duplicated as a role table in the application database.

## Consequences

### Positive

- One identity store, one place to manage users/roles — no duplicated credentials or user tables across frontend, backend, and CLI
- The backend's authorization logic is declarative (`@RolesAllowed`) rather than hand-rolled
- New clients (e.g. a future mobile app) integrate against the same realm with no backend changes

### Negative / Risks

- All three clients have a hard runtime dependency on Keycloak being reachable
- Local development requires Docker/Podman so Quarkus Dev Services can start a Keycloak instance

## Alternatives Considered

| Alternative | Reason not adopted |
|---|---|
| Per-service session/JWT auth, hand-rolled | Duplicates login and token logic across three codebases; no SSO between web and CLI |
| Third-party IDaaS (Auth0, etc.) | Keycloak is open-source, self-hostable on the same OpenShift cluster the app deploys to, and is the standard IdP already used across this environment's other projects |
