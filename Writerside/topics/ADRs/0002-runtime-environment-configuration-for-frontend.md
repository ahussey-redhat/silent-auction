# ADR-0002: Runtime Environment Configuration for the Frontend

| Field | Value |
|---|---|
| **Status** | Accepted |

---

## Context

The frontend is deployed as a container image via `silent-auction-demo-infra`'s GitOps pipeline, promoted across environments (e.g. dev-spaces sandbox, production) without rebuilding. Next.js's default pattern — reading `process.env` at build time and inlining values into the client bundle — would mean baking one environment's `BACKEND_URL`, Keycloak settings, and `BID_INCREMENT` into the image, requiring a rebuild per environment. That conflicts with the GitOps model, where the same artifact should be promotable and configuration should come from the deployment target (Kubernetes ConfigMaps/Secrets), not the build.

`BACKEND_URL` in particular must never reach the browser — it's a server-side-only value.

## Decision

Runtime configuration is served by a Next.js **API route**, `/api/frontend/config`, which reads `process.env` at **request time** (not build time) and returns the subset of config the browser needs. The client-side `ConfigProvider` fetches this route on load and exposes it via `useConfig()`; every other provider (`AuthProvider`, `AuctionsProvider`, etc.) waits on it before initializing.

In production/Kubernetes, environment variables are injected at the container level (e.g. via the demo-infra Kustomize overlays' ConfigMaps/Secrets) and read by the Next.js standalone server at startup — no image rebuild required. In local development, the same variables are read from `.env`.

## Consequences

### Positive

- One container image is built once and promoted across every environment, consistent with the demo-infra GitOps layering ([ADR-0003](0003-layered-gitops-deployment-via-argocd-app-of-apps.md))
- `BACKEND_URL` and other server-only values never get compiled into the client bundle
- Environment-specific config changes (e.g. a new Keycloak realm) are a deployment change, not a code change

### Negative / Risks

- Adds a network round trip to `/api/frontend/config` before the app can initialize auth — a slower first paint than build-time inlining
- The app has a hard dependency on this route responding correctly; a misconfigured deployment fails at startup rather than at build time, which pushes some errors later in the pipeline

## Alternatives Considered

| Alternative | Reason not adopted |
|---|---|
| Build-time env vars (`NEXT_PUBLIC_*`, `publicRuntimeConfig`) | Requires a distinct image per environment; breaks the "build once, promote everywhere" GitOps model |
| Client fetches config directly from the backend | Would require the backend to expose frontend-specific config, coupling an unrelated concern into the domain API |
