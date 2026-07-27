# ADR-0003: Layered GitOps Deployment via ArgoCD App-of-Apps

| Field | Value |
|---|---|
| **Status** | Accepted |

---

## Context

Getting Silent Auction running on an OpenShift cluster (ROSA, ARO, or bare metal) involves several concerns with genuinely different lifecycles:

- **Bootstrapping** GitOps tooling onto a cluster that doesn't have it yet — a one-time, imperative step
- **Cluster-scoped operators** the stack depends on — Crunchy Data (PostgreSQL), OpenShift Pipelines (Tekton), OpenShift Dev Spaces, secrets management — installed once per cluster, changed rarely
- **Application workloads** — the actual Postgres instances, app deployments, routes, and secrets — which differ per environment and change far more often

Mixing these into one flat set of manifests makes it hard to reason about blast radius (an operator upgrade shouldn't be indistinguishable from an app config change) and hard to add a second environment without duplicating operator definitions.

## Decision

`silent-auction-demo-infra` is structured as four ordered layers, forming an ArgoCD **app-of-apps**:

1. **`1-bootstrap`** — an Ansible playbook (`run_playbook.sh`) that installs ArgoCD/OpenShift GitOps onto a fresh cluster. Imperative, run once per cluster.
2. **`2-environments`** — per-environment ArgoCD `Application` manifests (e.g. `rosa/`) that point ArgoCD at layers 3 and 4 for that environment.
3. **`3-operators`** — Kustomize base/overlays installing the cluster-scoped operators (Crunchy Data, Dev Spaces, Pipelines, misc, secrets).
4. **`4-workloads`** — Kustomize overlays per environment (`devspaces`, `production`) for the actual application resources: Crunchy PostgreSQL instances, DB init, pgAdmin, routes, ConfigMaps/Secrets, service accounts, and Argo Rollouts definitions.

Layers 2–4 are declarative and continuously reconciled by ArgoCD; only layer 1 is imperative and run by hand against a new cluster.

## Consequences

### Positive

- Clear separation between one-time cluster bootstrap and continuously-reconciled application state
- Operators and workloads can be changed, reviewed, and rolled back independently
- Adding a new environment means extending layer 2 (and adding a `4-workloads` overlay) without touching operator definitions
- Consistent with how the frontend's runtime config ([ADR-0002](0002-runtime-environment-configuration-for-frontend.md)) expects environment-specific values to arrive via the deployment layer, not the image

### Negative / Risks

- Four layers is more structure than a small demo strictly needs — there's a learning curve for anyone new to the repo
- Understanding the full deployment requires familiarity with both Ansible (layer 1) and Kustomize/ArgoCD (layers 2–4)

## Alternatives Considered

| Alternative | Reason not adopted |
|---|---|
| Flat manifests applied via `oc apply` | No continuous reconciliation or drift detection; no clean separation between bootstrap, operators, and workloads |
| Helm charts per component | Kustomize overlays give simpler per-environment patching without introducing templating, and stay consistent with the app-of-apps pattern used throughout |
