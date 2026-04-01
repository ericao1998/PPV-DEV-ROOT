# Workspace Repo Restructure - Issue Registry

## Open

| ID | Title | Status | Priority | Notes |
|---|---|---|---|---|
| WRR-001 | Create root-level architecture lane for repo restructure | Complete | High | Canonical docs created under `C:\Dev\Architect-Docs\Workspace-Repo-Restructure`. |
| WRR-002 | Move `PPV_Website` to `C:\Dev\PPV_Website` | Complete | Critical | Nested git repo moved intact. |
| WRR-003 | Move `lead-crawler` to `C:\Dev\lead-crawler` | Complete | Critical | Crawler promoted to top-level sibling structure. |
| WRR-004 | Initialize top-level git repo for `lead-crawler` | Complete | High | New local git repo initialized at the top-level path. |
| WRR-005 | Update root `.gitignore` and workspace docs for new top-level paths | In progress | High | Root docs/ignores updated; more follow-up may remain in repo-local docs. |
| WRR-006 | Update Azure repo docs to remove website/crawler parent-boundary assumptions | In progress | High | High-level Azure docs updated; deeper CI ownership cleanup remains. |
| WRR-007 | Update workspace rituals/path examples that still point to nested `PPV_Website`/`lead-crawler` | In progress | Medium | Root ritual/examples updated; residual repo-local historical references remain. |

## Future

| ID | Title | Status | Priority | Notes |
|---|---|---|---|---|
| WRR-F01 | Convert child repos to formal submodules under `PPV Dev Root` | Not started | Medium | Better than the current ignore-only umbrella model. |
| WRR-F02 | Preserve filtered git history for `lead-crawler` from `azure-portal-dev` | Not started | Medium | Useful if crawler repo history becomes important later. |
