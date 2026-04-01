# Avado Agentic Workflow

Last updated: 2026-04-01

## Purpose

This document defines the working convention for using `avado` as the primary PPV agentic development host.

The goal is:

- Hermes runs on `avado`
- active git repos live on `avado`
- VS Code connects to `avado`
- clean development repos are separated from live runtime directories

## Core Rule

Use the clean git clones on `avado` for development and agent work.

Do not use the live runtime directories as the default editing targets.

## Canonical Repo Paths On Avado

Use these as the default working paths:

- Root workspace docs: `/home/eao168/PPV-Dev-Root`
- Website and ads repo: `/home/eao168/PPV_Website`
- Azure/shared infra repo: `/home/eao168/azure-portal-dev`
- Memory agent repo: `/home/eao168/salesforce-memory-agent`
- Salesforce repo: `/home/eao168/prime-salesforce-dev`
- Clean crawler repo: `/home/eao168/Lead-Crawler`

## Runtime Folders That Are Not Default Dev Targets

These currently appear to be live runtime or server-working-copy directories.

Do not treat them as the default Hermes editing targets:

- `/home/eao168/lead-crawler`
- `/home/eao168/ads-ops-job`

Reason:

- they are tied to currently running jobs
- they are not clean git clones
- editing them directly increases drift and deployment risk

## Hermes Working Convention

Hermes should be launched on `avado` and pointed at the clean repo clones.

Default repo choices by task:

- strategy, runbooks, migration notes: `/home/eao168/PPV-Dev-Root`
- website, ads ops, event router, lead intake: `/home/eao168/PPV_Website`
- crawler code changes: `/home/eao168/Lead-Crawler`
- shared infra/orchestration: `/home/eao168/azure-portal-dev`
- VS Code extension / memory tooling: `/home/eao168/salesforce-memory-agent`
- Salesforce work: `/home/eao168/prime-salesforce-dev`

## VS Code Convention

Preferred workflow:

1. SSH into `avado` with VS Code Remote SSH
2. Open the clean repo you actually want to work on
3. Use Hermes in that same remote environment

Do not default to opening `/home/eao168` as one giant mutable workspace for editing.

That top-level home directory can still be useful for browsing, but the actual working unit should be the clean target repo.

## Deployment / Runtime Separation

Current separation target:

- clean clone = development and agent workspace
- runtime folder = service execution target

Near-term rule:

- develop in clean clones
- cut over runtime directories deliberately
- do not let live folders become the canonical source of truth

Medium-term target:

- containerize production jobs where practical
- treat runtime as deployment output, not as the primary editable working tree

## Immediate Default Choices

Until further cleanup work is complete, use these defaults:

- Hermes default coding repo for crawler work: `/home/eao168/Lead-Crawler`
- Hermes default coding repo for ads/site work: `/home/eao168/PPV_Website`
- Hermes default repo for workspace docs and operating notes: `/home/eao168/PPV-Dev-Root`

## Safety Rule For Current Session

Before any change to crawler or ads runtime behavior on `avado`, confirm whether the target path is:

- a clean git clone, or
- a live runtime directory

If it is a live runtime directory, do not modify it casually.

## What “Moved To Avado” Means

The phrase “move repos to avado” means:

- the clean git-managed working copies now live on `avado`
- git pull/push and agentic editing should happen there
- recovery for that environment is backed by Synology offsite backup

It does not mean:

- immediately replacing every live runtime directory
- editing running service folders by default

