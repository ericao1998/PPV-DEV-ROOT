# Workspace Repo Restructure - Active Handoff

Last Updated: 2026-04-01
Status: Active

## Current Lane
- repo-boundary cleanup and workspace normalization

## Current Scope
- document the migration
- move `PPV_Website` and `lead-crawler` to top-level workspace siblings
- clean up root/Azure path references

## Next Actions
1. create the root architecture lane
2. move `PPV_Website`
3. move `lead-crawler`
4. initialize crawler repo at top level
5. update root/Azure docs and ignore rules
6. validate all repo states

Checkpoint 2026-04-01:
- Scope: created canonical root docs for the workspace repo-boundary migration
- Changed: added plan, issue registry, active handoff, and process docs under `C:\Dev\Architect-Docs\Workspace-Repo-Restructure`
- Validated: confirmed `PPV_Website` is already a nested git repo while `lead-crawler` is still just a directory under `azure-portal-dev`
- Blocked: `lead-crawler` does not yet have its own remote/history path in this pass
- Next: execute the filesystem moves and clean up doc/path references

Checkpoint 2026-04-01:
- Scope: executed the workspace restructure and path cleanup
- Changed: moved `PPV_Website` to `C:\Dev\PPV_Website`, moved `lead-crawler` to `C:\Dev\lead-crawler`, initialized the new crawler repo, and updated root/Azure path references and ignore rules
- Validated: root now sees the new top-level structure; website repo remains intact; crawler is now a standalone git repo; Azure repo now shows the expected crawler deletions
- Blocked: Azure CI ownership cleanup is not finished, `lead-crawler` does not yet have a remote, and many repo-local historical doc references still point to old paths
- Next: decide whether to commit the root and Azure restructure docs now, and then plan remote setup/submodule strategy for the new crawler repo
