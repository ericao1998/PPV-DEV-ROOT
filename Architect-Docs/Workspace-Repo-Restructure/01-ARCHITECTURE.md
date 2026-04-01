# Workspace Repo Restructure - Architecture

Last Updated: 2026-04-01
Status: Active

## Goal
Normalize the `C:\Dev` workspace so application/product repos live as top-level siblings under `PPV Dev Root`, while the root repo remains the governance umbrella.

## Target Structure
- `C:\Dev\prime-salesforce-dev`
- `C:\Dev\PPV_Website`
- `C:\Dev\lead-crawler`
- `C:\Dev\azure-portal-dev`
- `C:\Dev\salesforce-memory-agent`

## Boundary Model
- `PPV Dev Root` (`C:\Dev`) owns workspace governance, repo map, shared operating rules, and cross-repo architecture.
- `prime-salesforce-dev` owns Salesforce product/application concerns.
- `PPV_Website` owns website/application concerns.
- `lead-crawler` owns crawler/data pipeline concerns.
- `azure-portal-dev` owns shared Azure infra/orchestration concerns rather than acting as the parent of product repos.

## Migration Principles
- prefer clean repo boundaries over nested-repo ambiguity
- preserve `PPV_Website` git history by moving the repo directory intact
- promote `lead-crawler` into its own working repo at the new top-level path
- keep the root repo from accidentally absorbing child repo files via `.gitignore`
- update docs/path references in the same migration session

## Current Constraints
- `PPV_Website` is already a real nested git repo under `azure-portal-dev`
- `lead-crawler` is currently a directory inside `azure-portal-dev`, not its own repo
- `azure-portal-dev` currently has unrelated uncommitted changes, so this migration prioritizes structural cleanup over full history extraction for `lead-crawler`

## Immediate Design Decision
This pass creates the clean top-level structure now.

Future improvement:
- optionally convert child repos to formal submodules under `PPV Dev Root`
- optionally extract/preserve filtered history for `lead-crawler` if needed beyond this initial separation
