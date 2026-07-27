# Silent Auction

Silent Auction is a full-stack demo application: a web frontend, a REST/WebSocket backend, a CLI, and a GitOps deployment pipeline that ships it all to OpenShift.

Bidders browse open auctions, place bids, and watch the highest bid update live. Administrators create auctions and manage users. The same domain — auctions, bids, users — is exposed identically through the web UI, the CLI, and the backend's own REST API, all sitting behind a single Keycloak realm.

## Repositories

| Repo | Purpose |
|---|---|
| [`silent-auction-backend`](https://github.com/ahenshaw-work/silent-auction-backend) | Quarkus REST + WebSocket API, PostgreSQL, Keycloak/OIDC |
| [`silent-auction-frontend`](https://github.com/ahenshaw-work/silent-auction-frontend) | Next.js + PatternFly web app |
| [`silent-auction-cli`](https://github.com/ahenshaw-work/silent-auction-cli) | Python/Typer CLI client for the backend API |
| [`silent-auction-demo-infra`](https://github.com/ahenshaw-work/silent-auction-demo-infra) | GitOps: bootstraps and deploys the stack to OpenShift (ROSA/ARO/bare metal) via ArgoCD |
| `silent-auction` | This repo — architecture, ADRs, and component documentation |

See [Architecture](architecture.md) for how these fit together, and [Components](components.md) for a closer look at each repo.

## Architecture Decision Records

Notable as-built architecture decisions — the ones that shape how the components talk to each other — are tracked as ADRs. See [ADRs](ADRs.md).