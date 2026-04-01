# AI-HANDOFF - Workspace Repo Restructure

Last Updated: 2026-04-01
Owners: Codex (state/gates), Claude (implementation/evidence)

## Current State
- Phase: implementation planning + workspace restructure
- Goal: promote `PPV_Website` and `lead-crawler` to top-level siblings under `C:\Dev`

## Validation Summary

| Item | Status | Evidence |
|---|---|---|
| Root architecture lane created | Complete | `Architect-Docs/Workspace-Repo-Restructure/**` |
| `PPV_Website` move completed | Complete | moved intact to `C:\Dev\PPV_Website` |
| `lead-crawler` move completed | Complete | moved to `C:\Dev\lead-crawler` |
| `lead-crawler` repo initialized | Complete | `git init -b main` run in new top-level crawler path |
| Root docs/path references updated | In progress | root `.gitignore`, `AGENTS.md`, `CODEBASE-MAP.md`, and `SESSION-RITUAL.md` updated |
| Azure docs/path references updated | In progress | high-level `AGENTS.md`, `CICD.md`, and `SESSION_HANDOFF.md` updated |

## Open Items
- [ ] Move `PPV_Website` out of `azure-portal-dev`
- [ ] Move `lead-crawler` out of `azure-portal-dev`
- [ ] Initialize `lead-crawler` as a top-level repo
- [ ] Update root `.gitignore`
- [ ] Update stale workspace ritual/path references
- [ ] Update Azure repo docs to reflect new boundaries
- [ ] Decide whether to commit root/Azure restructure artifacts now or in a follow-up pass
- [ ] Set remote and long-term repo strategy for `lead-crawler`
