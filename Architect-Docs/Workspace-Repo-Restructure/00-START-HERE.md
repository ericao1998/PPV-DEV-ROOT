# Workspace Repo Restructure - Start Here

Last Updated: 2026-04-01
Status: Active

## Purpose
This stack governs the workspace-level migration that promotes `PPV_Website` and `lead-crawler` to top-level siblings under `C:\Dev`, while keeping `PPV Dev Root` as the umbrella governance repo.

## Scope
- create a root-level architecture trail for the repo-boundary migration
- move `PPV_Website` out of `azure-portal-dev`
- move `lead-crawler` out of `azure-portal-dev`
- keep `azure-portal-dev` focused on shared Azure infra/orchestration concerns
- update root and Azure repo docs/path references to match the new structure

## Out Of Scope
- code refactors inside website/crawler applications
- CI/CD redesign beyond path/reference cleanup
- remote repo creation or remote migration for `lead-crawler`
- formal submodule conversion for all child repos in this same pass

## Canonical Docs
- architecture: [01-ARCHITECTURE.md](C:/Dev/Architect-Docs/Workspace-Repo-Restructure/01-ARCHITECTURE.md)
- runbooks: [02-RUNBOOKS.md](C:/Dev/Architect-Docs/Workspace-Repo-Restructure/02-RUNBOOKS.md)
- issues: [03-ISSUE-REGISTRY.md](C:/Dev/Architect-Docs/Workspace-Repo-Restructure/03-ISSUE-REGISTRY.md)
- active handoff: [04-ACTIVE-HANDOFF.md](C:/Dev/Architect-Docs/Workspace-Repo-Restructure/04-ACTIVE-HANDOFF.md)
- AI handoff: [AI-HANDOFF.md](C:/Dev/Architect-Docs/Workspace-Repo-Restructure/AI-HANDOFF.md)
- process runbook: [PROCESS-RUNBOOK.md](C:/Dev/Architect-Docs/Workspace-Repo-Restructure/PROCESS-RUNBOOK.md)
- index: [DOCS-INDEX.md](C:/Dev/Architect-Docs/Workspace-Repo-Restructure/DOCS-INDEX.md)
