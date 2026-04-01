# Session Ritual - Prime Property Ventures (PPV)

> **Read this first.** This document orients any AI assistant to the workspace structure, active work, and governance rules.

## Workspace Overview

**Business Domain:** Real estate lead management, agent productivity, and outbound sales enablement
**Tech Stack:** Salesforce (Apex, LWC, Flows), Azure (Container Apps, AOAI), Python, Node.js

## Business Context Bootstrap

All AI sessions must also read `C:\Dev\BUSINESS-CONTEXT.md`.

That document is the root business brief for this workspace and exists to keep all sessions aligned on PPV's real objective:
- acquire property below market by solving seller problems
- identify prospect issues, challenges, and needs before proposing messaging, targeting, automation, or UX changes
- optimize for qualified seller opportunities, not shallow traffic or lead volume

If a session ignores seller-problem fit, it is likely optimizing the wrong thing.

---

## Repository Map (3 Git Repos)

| Repository | Current Branch | Purpose | Key Branches |
|------------|----------------|---------|--------------|
| **prime-salesforce-dev** | Lead-Ingestion | Main Salesforce DX project - Apex, LWC, Flows | main, Performance-Hub, power-dialer-dev, embeddedDialer-dev |
| **azure-portal-dev** | Crawler | Lead crawler, data pipelines, PPV Website | main, PPV-Website, PPV-Website-Dev, suffolk-only |
| **salesforce-memory-agent** | main | VS Code extension for AI-assisted development | main |

---

## Current Sprint/Focus Areas

### prime-salesforce-dev - Active Work
- **Lead Ingestion** (current branch) - Lead import and processing
- **PowerDialer Open Bugs:**
  - DNC modal: dispo modal pops up and disappears (also from VM drop)
  - After last number dialed, loops back to available number even after VM
  - Status not settable from modal path
- **KPI Report Setup Pending:**
  - GA4 credentials missing (need `GA4_CREDENTIALS_JSON_B64`)
  - Google Ads API disabled (project `745336976478`)
  - Meta token validity needs confirmation

### azure-portal-dev - Active Work
- **Crawler** (current branch) - Lead data crawling and normalization
- **PPV Website** - Company website development (`PPV-Website` branch)
- AOAI integration for address extraction

### salesforce-memory-agent
- Mini MVP complete
- Planned: cloud sync, sub-agent systems

---

## Governance Rules (Summary)

> **Full governance doc: `GOVERNANCE.md`** - Read this for complete role definitions.

### CODEX (Architect)
| CAN DO | CANNOT DO |
|--------|-----------|
| Read/analyze code | Write/edit files |
| Review changes | Deploy code |
| Design architecture | Run git commit/push |
| Approve phase gates | Skip to implementation |
| Recommend solutions | Modify any file |

### CLAUDE (Coder)
| CAN DO (after approval) | CANNOT DO |
|-------------------------|-----------|
| Write/edit code | Start without gate approval |
| Run tests & deploy | Skip phases |
| Git commit/push | Modify frozen components |
| Implement features | Make architecture decisions |
| Fix bugs | Ignore Codex direction |

### Codex Exception — Plan Docs

Codex is permitted to edit **planning/governance documents only** (e.g., workspace root governance docs, repo handoff notes, `Dev Progress Docs/**`) without switching roles. All other files remain read-only for Codex.

**Constraints:**
- No code, metadata, or deployment artifacts may be edited by Codex.
- All edits must be limited to planning, governance, and documentation of work.
- Phase tokens still required before implementation work begins.

### Phase Flow
```
Discovery → [Codex Gate] → Design → [Codex Gate] → Implementation → [Codex Gate] → Testing → [Codex Gate] → Deployment
```

| Rule | Description |
|------|-------------|
| **Default Mode** | Review-only - no modifications without explicit approval |
| **Test Coverage** | 85%+ required for new/changed Apex |
| **Philosophy** | Reuse-first - search existing code before writing new |

### Frozen Components (No Modifications Without Written Approval)

**Apex Classes:**
- PowerDialerController, PowerDialerHandler, SmrtPhoneWebhookHandler
- TwilioPhoneController, TwilioVFPageController, CallRecordingHandler
- DispositionHandler, MessageAutomationHandler
- SMSManualSendFlowAction, SMSScheduledExecutor, SMSTemplateTrigger, SMSTemplateTriggerHandler
- EmailSendQueueable, EmailManualSendFlowAction
- RecordInfoFetcher, InteractionTimelineController

**LWC Components:**
- hotLeads, twilioVoiceContainer, twilioPhoneStatusBar, embeddedDialer, smsMessenger

**Visualforce:**
- TwilioVoiceBridge.page

---

## CI/CD Summary

> **Each repository has a `CICD.md` file** - Read it when working in that repo.

| Repository | CI/CD Type | Trigger |
|------------|------------|---------|
| **prime-salesforce-dev** | Manual SF CLI | No auto-deploy. LWC = no tests, Apex = tests required |
| **azure-portal-dev** | GitHub Actions | Auto-deploy on push to `PPV-Website` or `Crawler` branches |
| **salesforce-memory-agent** | None | Local dev only, F5 to test |

---

## Domain Routing (prime-salesforce-dev)

> **Ask first: "Is this Phone, Lead, Email, SMS, or KPI related?"**

| Keywords | Look Here | Frozen? |
|----------|-----------|---------|
| phone, dial, call, twilio, VM | `Phone-Stack/`, `embeddedDialer`, `twilioPhoneStatusBar` | PowerDialerController=YES |
| lead, hot lead, disposition | `Leads/`, `hotLeads` | hotLeads=YES |
| email, template | `Email-Stack/` | EmailSendQueueable=YES |
| sms, text, message | `SMS/`, `smsMessenger` | smsMessenger=YES |
| kpi, dashboard, metrics | `Ops-Performance-Hub/` | No |
| timeline, activity | `Front-End-UI/` | InteractionTimelineController=YES |

**Reuse these first:** `TestUtility`, `GlobalConstants`, `RecordInfoFetcher`, `DebugConfig`

> **Full map: `CODEBASE-MAP.md`** - Component dependencies, message contracts, gotchas

---

## Key Documentation

| Document | Location | Purpose |
|----------|----------|---------|
| **GOVERNANCE.md** | Workspace root (`c:\Dev\`) | Codex/Claude roles, phase gates, permissions |
| **CODEBASE-MAP.md** | Workspace root (`c:\Dev\`) | Domain routing, dependencies, patterns |
| **BUSINESS-CONTEXT.md** | Workspace root (`c:\Dev\`) | Root PPV business model, seller-problem framing, and lead-quality standard for all AI sessions |
| **UI-DESIGN-PRINCIPLES.md** | Workspace root (`c:\Dev\`) | Org design standard for internal dashboards, admin tools, and operator UX |
| **AGENTS.md** | `prime-salesforce-dev/AGENTS.md` | Full protocol, safety rules, specialized agents |
| **CLAUDE.md** | `prime-salesforce-dev/CLAUDE.md` | Quick development rules |
| **CICD.md** | Each repo root | CI/CD process for that repository |
| **REPOSITORY-STRUCTURE.md** | `prime-salesforce-dev/REPOSITORY-STRUCTURE.md` | Codebase navigation |
| **CODEBASE-STANDARDS-GUIDE.md** | `prime-salesforce-dev/CODEBASE-STANDARDS-GUIDE.md` | Coding standards |

---

## UI Design Bootstrap Rule

If the task involves any of the following, read `UI-DESIGN-PRINCIPLES.md` before design or implementation:

- dashboards
- internal admin tools
- operator consoles
- settings surfaces
- navigation/layout/theming decisions

---

## WSL / Windows Git Interop

When the session is running in `bash` under `/mnt/c/...`, Git auth can fail if the repo uses an HTTPS remote and the configured credential helper points to Windows Git Credential Manager.

### Bootstrap Check

Run these before any auth-sensitive Git command (`push`, `pull`, `fetch`, `clone`):

```bash
pwd
git remote -v
git config --get credential.helper
```

### Rule

If all of the following are true:

- current shell is `bash`
- working tree is under `/mnt/c/...`
- Git remote is `https://github.com/...`
- `git config --get credential.helper` resolves to Windows Git Credential Manager

Then:

- use normal `git` in `bash` for read-only commands like `status`, `diff`, `log`, `branch`
- use `cmd.exe /c "git -C C:\\... <command>"` for auth-sensitive Git commands like `push`, `pull`, `fetch`

### Known Good Pattern

```bash
cmd.exe /c "git -C C:\Dev\PPV_Website push origin PPV-Website"
```

This rule applies to both Codex and Claude and should be assumed at session load whenever the environment matches the conditions above.

---

## Runtime Command Map

This workspace mixes Linux-shell execution with some Windows-installed tooling. Do not rediscover command names during the task; normalize them at session load.

### Bootstrap Check

Run these once near the start of the session:

```bash
command -v python || true
command -v python3 || true
command -v node || true
command -v npm || true
command -v az || true
python3 --version || true
node --version || true
npm --version || true
```

### Known Good Defaults For This Workspace

- `python`: do not assume it exists in `bash`
- `python3`: preferred Python interpreter in `bash`
- `node`: available natively in the Linux user path
- `npm`: available natively in the Linux user path
- `az`: available and callable from the mounted Windows install path

### Rule

- if a command example says `python` and the session is running in `bash`, use `python3` unless the repo explicitly requires something else
- keep using native `node` and `npm` in `bash`
- keep using `az` in `bash` unless a task proves a Windows-shell-only auth or path issue
- for auth-sensitive Git commands, apply the WSL / Windows Git Interop rule above

### Known Good Examples

```bash
python3 script.py
python3 -m pytest
npm run build
az account show
cmd.exe /c "git -C C:\Dev\PPV_Website push origin PPV-Website"
```

This rule applies to both Codex and Claude and should be treated as baseline workspace knowledge.

---

## Session Start Checklist

AI assistant should confirm at session start:

1. [ ] Read this SESSION-RITUAL.md
2. [ ] Read `GOVERNANCE.md` - understand Codex/Claude roles
3. [ ] Read `CODEBASE-MAP.md` - understand domain routing and patterns
4. [ ] Read `BUSINESS-CONTEXT.md` - align to PPV's seller-problem and acquisition model
5. [ ] Acknowledge frozen components cannot be modified
6. [ ] Confirm your role: **"I am [Codex/Claude]. My role is [architect/coder]."**
7. [ ] State: **"I will prioritize reuse over creation and respect frozen components."**
8. [ ] Ask: **"Which repository/branch is the focus for this session?"**
9. [ ] Ask: **"Is this Phone, Lead, Email, SMS, or KPI related?"** (for domain routing)
10. [ ] **Read the `CICD.md` in the focused repository** before any deployment work
11. [ ] **Validate org access before any org test/deploy work**:
    - `sf --version`
    - `sf org list --all` and confirm `ppv-prod` is `Connected`
    - If PATH is missing `sf/node`, use explicit paths (`C:\Program Files\nodejs` + `C:\Users\ao219\AppData\Roaming\npm\sf.cmd`)
12. [ ] **Run the WSL/Windows Git interop bootstrap check** before any auth-sensitive Git command:
    - `pwd`
    - `git remote -v`
    - `git config --get credential.helper`
    - If running in `bash` under `/mnt/c/...` with HTTPS GitHub remote + Windows credential helper, switch auth-sensitive Git commands to `cmd.exe /c "git -C C:\\... <command>"`
13. [ ] **Run the runtime command bootstrap check** and normalize common commands:
    - `command -v python || true`
    - `command -v python3 || true`
    - `command -v node || true`
    - `command -v npm || true`
    - `command -v az || true`
    - Prefer `python3` over `python` in `bash`
14. [ ] **If Codex is handing implementation work to Claude, provide a copy-paste-ready scope block by default**
    - include gate statement
    - include repository/branch and task ID
    - include allowed write scope
    - include explicit out-of-scope items
    - include validation and required doc/handoff updates

## Auto-Confirm (Session Start Output)

Gate: Starting Discovery. Constraints confirmed. Claude may not code yet.
I understand the governance model. Codex is the architect and approval gate. Claude is the coder and implements only after approval. I will not skip phases or modify frozen components without explicit approval.
Role: I am Codex. My role is architect/reviewer only; no file edits or deployments.
Defaults: review-only, reuse-first, 85%+ Apex coverage for changes, frozen components are off-limits without written approval.
Business context: PPV acquires property below market by solving seller problems; optimize for qualified seller need and business fit, not shallow volume.
Org access: validate `sf` runtime and `ppv-prod` connectivity before any org-facing test/deploy step.

---

## Quick Commands Reference

```bash
# Salesforce deployment (with Apex)
sf project deploy start --target-org ppv-prod --source-dir <path> --test-level RunLocalTests --wait 60

# LWC-only deployment (no tests needed)
sf project deploy start --target-org ppv-prod --source-dir force-app/main/default/lwc/<component> --wait 60

# Check current branch
git branch --show-current

# WSL/Windows Git auth-safe push pattern
cmd.exe /c "git -C C:\Dev\PPV_Website push origin PPV-Website"

# Python in this workspace
python3 --version
python3 script.py

# Azure Container App build (run from repo subdirectory to minimize context)
cd lead-crawler
az acr build -r ppvleadacr -t <tag> -f normalize_job/Dockerfile .
az containerapp job update -n normalize-job -g PPV_Salesforce --image <tag>
```

---

*Last updated: 2026-03-11 | Migrated: OneDrive → c:\Dev workspace*
