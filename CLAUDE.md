# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This repository is the **architecture and documentation hub** for **Silent Auction** — a demo silent-auction application (web frontend, REST/WebSocket backend, CLI, and a GitOps-deployed OpenShift environment). Application source code lives in separate per-component repositories; this repo contains Writerside docs and Architecture Decision Records (ADRs) that describe how those components fit together.

## Documentation

Docs are maintained with **JetBrains Writerside** in `Writerside/`. `sa.tree` defines the navigation structure; topic files live under `Writerside/topics/`. ADRs live under `Writerside/topics/ADRs/`.

Docs build via `.github/workflows/build-docs.yaml` (JetBrains Writerside GitHub Action) and publish to GitHub Pages on push to `main`.

## Repositories

| Repo | Purpose | Stack |
|---|---|---|
| [`silent-auction-backend`](https://github.com/ahenshaw-work/silent-auction-backend) | REST + WebSocket API | Quarkus 3 / Java 17, PostgreSQL, Keycloak/OIDC |
| [`silent-auction-frontend`](https://github.com/ahenshaw-work/silent-auction-frontend) | Web app | Next.js 15, React 18, PatternFly 6, TypeScript |
| [`silent-auction-cli`](https://github.com/ahenshaw-work/silent-auction-cli) | CLI client | Python, Typer, python-keycloak |
| [`silent-auction-demo-infra`](https://github.com/ahenshaw-work/silent-auction-demo-infra) | GitOps deployment | Ansible (bootstrap), Kustomize + ArgoCD (OpenShift/ROSA) |
| `silent-auction` | This repo | Writerside docs, ADRs |

Full architecture: [`Writerside/topics/architecture.md`](Writerside/topics/architecture.md). ADR index: [`Writerside/topics/ADRs.md`](Writerside/topics/ADRs.md).

## Working in This Repo

This repo has no application source or build/test commands — it's documentation only. When making architectural claims, verify against the relevant component repo (endpoints, config, folder layout) rather than assuming — those repos are the source of truth and can drift out of sync with these docs.

If a change here reflects a real architectural decision (not just fixing a typo), consider whether it warrants a new ADR under `Writerside/topics/ADRs/`, following the existing numbering and format (Context / Decision / Consequences / Alternatives Considered).
