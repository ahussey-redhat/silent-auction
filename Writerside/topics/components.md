# Components

The Silent Auction application is made up of three domain concepts, each exposed the same way through every client:

1. [Auctions](auctions.md)
2. [Bids](bids.md)
3. [Users](users.md)

## Repositories

### [`silent-auction-backend`](https://github.com/ahenshaw-work/silent-auction-backend)

Quarkus 3 / Java 17 REST + WebSocket API. PostgreSQL persistence (Hibernate ORM, Liquibase migrations), Keycloak/OIDC authorization. The single source of truth for domain state — see [Architecture](architecture.md).

### [`silent-auction-frontend`](https://github.com/ahenshaw-work/silent-auction-frontend)

Next.js 15 + PatternFly 6 web app. Auction browsing, bidding, a live highest-bids leaderboard, and admin-only user management. Authenticates against Keycloak via PKCE.

### [`silent-auction-cli`](https://github.com/ahenshaw-work/silent-auction-cli)

Python/Typer command-line client exposing the same auction, bid, and user operations as the frontend, for scripting and terminal-based demos.

### [`silent-auction-demo-infra`](https://github.com/ahenshaw-work/silent-auction-demo-infra)

GitOps repo that deploys the full stack to OpenShift (ROSA/ARO/bare metal) via ArgoCD, layered as bootstrap → environments → operators → workloads.

> For how these components interact — including the shared Keycloak realm and CI/CD flow — see [Architecture](architecture.md).