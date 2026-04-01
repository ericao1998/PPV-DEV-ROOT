# Workspace Repo Restructure - Process Runbook

Last Updated: 2026-04-01
Status: Active

## Sequence
1. create root-level migration docs
2. inspect current nested repo state
3. move `PPV_Website` intact
4. move `lead-crawler`
5. initialize crawler repo
6. update root and Azure doc/path references
7. validate git state for root, Azure, website, and crawler

## Validation
- `C:\Dev\PPV_Website\.git` exists after move
- `C:\Dev\lead-crawler\.git` exists after setup
- `azure-portal-dev` no longer contains `PPV_Website/` or `lead-crawler/`
- root docs no longer reference nested paths
- workspace `.gitignore` shields child repos/directories from accidental root tracking
