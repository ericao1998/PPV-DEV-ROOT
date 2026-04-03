# PPV-Operator-OS Workspace Realignment Plan

> For Hermes: use subagent-driven-development for execution once this plan is approved task-by-task.

Goal: establish `PPV-Operator-OS` as the canonical operator/control-plane/app-factory repo, while keeping `PPV-Dev-Root` as the separate governance/session-loader host and keeping subsystem repos bounded.

Architecture: this is a governance-and-ownership migration first, not a monorepo merge. `PPV-Operator-OS` becomes the product/operator layer that observes, coordinates, and acts across bounded subsystem repos through explicit contracts. `PPV-Dev-Root` remains outside that stack as the workspace bootstrap, session-routing, and governance shell.

Tech stack: git, markdown docs, repo-local AGENTS/Architect-Docs conventions, existing PPV subsystem repos.

---

## Confirmed target workspace model

### Top-level workspace roles
- `PPV-Dev-Root`
  - stays separate
  - owns session bootstrap, governance, repo routing, global operating rules, workspace maps
- `PPV-Operator-OS`
  - new canonical repo
  - owns operator UI/app factory/control-plane/orchestration/contracts/higher-level tasking
- bounded subsystem repos under Operator-OS governance
  - `Lead-Crawler` / `lead-crawler`
  - `prime-salesforce-dev`
  - `PPV_Website`
  - `azure-portal-dev`
  - `salesforce-memory-agent` as supporting subsystem/tooling repo

### Out of PPV stack scope
These are not part of the PPV product-stack realignment unless explicitly pulled in later:
- `hermes-agent`
- `backup-scripts`
- `slack-bots`
- user dotfiles / tool caches / local infra folders

---

## Repo classification

### 1) Governance host (stays outside Operator-OS)
`/home/eao168/PPV-Dev-Root`

Purpose:
- workspace entrypoint
- governance and session rituals
- repo discovery and routing
- cross-repo operating contracts

Must not become:
- the app-factory implementation repo
- a runtime subsystem repo
- a catch-all product code home

### 2) Canonical operator/control-plane repo (new)
`/home/eao168/PPV-Operator-OS`

Purpose:
- app factory
- operator console
- orchestration/control plane
- subsystem contract definitions
- cross-domain status, triggering, routing, and evidence surfaces
- agent workflows and operator-facing autonomous loops

Owns:
- canonical app-factory docs
- operator surface UX
- cross-subsystem contracts
- workflow/task abstractions
- integration adapters that call into subsystems through bounded interfaces

Does not own by default:
- crawler internals
- Salesforce internal business logic
- public website implementation internals
- Azure environment internals

### 3) Bounded subsystem repos under Operator-OS governance

#### `/home/eao168/Lead-Crawler` and/or `/home/eao168/lead-crawler`
Owns:
- crawler runtime
- connector execution
- normalization / skip trace / SF sync pipeline mechanics
- crawler-specific persistence and job behavior

Migration effect:
- app-factory/operator-surface ownership moves up to `PPV-Operator-OS`
- crawler dashboard pieces that are really operator-OS concerns should migrate up over time
- repo remains bounded execution domain

#### `/home/eao168/prime-salesforce-dev`
Owns:
- Salesforce CRM domain
- Salesforce-side data contracts
- bounded integration hooks and approved mutation surfaces
- CRM-side business records and workflows

Migration effect:
- no longer a canonical home for operator dashboard/app-factory UX
- remains a managed subsystem controlled through bounded contracts

#### `/home/eao168/PPV_Website`
Owns:
- public web/app surfaces
- likely includes `prime-property-web`, `lead-intake-api`, `event-router`
- may include `ads-ops-job` if retained as website/product-adjacent workload

Migration effect:
- website remains a subsystem domain
- any internal operator/control-plane surfaces should live in `PPV-Operator-OS`, not here, unless intentionally public/product-facing

#### `/home/eao168/azure-portal-dev`
Owns:
- infra/orchestration/deployment support
- Azure-adjacent environment and shared infra assets

Migration effect:
- remains infra subsystem
- `PPV-Operator-OS` may consume its outputs/capabilities, but should not absorb infra internals by default

#### `/home/eao168/salesforce-memory-agent`
Owns:
- Salesforce memory/extension/tooling support

Migration effect:
- supporting subsystem under Operator-OS governance
- not a top-level product owner

---

## Recommended target topology

```text
/home/eao168/
├── PPV-Dev-Root                  # governance host; stays separate
│   ├── AGENTS.md
│   ├── GOVERNANCE.md
│   ├── SESSION-RITUAL.md
│   ├── CODEBASE-MAP.md
│   └── docs/plans/
│
├── PPV-Operator-OS               # new canonical operator/control-plane repo
│   ├── AGENTS.md
│   ├── README.md
│   ├── Architect-Docs/
│   │   └── Operator-OS/
│   │       ├── 00-START-HERE.md
│   │       ├── 01-ARCHITECTURE.md
│   │       ├── 02-RUNBOOKS.md
│   │       ├── 03-ISSUE-REGISTRY.md
│   │       ├── 04-ACTIVE-HANDOFF.md
│   │       ├── AI-HANDOFF.md
│   │       ├── PROCESS-RUNBOOK.md
│   │       └── DOCS-INDEX.md
│   ├── contracts/
│   │   ├── lead-crawler/
│   │   ├── salesforce/
│   │   ├── website/
│   │   └── infra/
│   ├── apps/
│   │   └── operator-console/
│   ├── services/
│   │   └── orchestration/
│   └── adapters/
│       ├── lead-crawler/
│       ├── salesforce/
│       ├── website/
│       └── azure/
│
├── Lead-Crawler                  # bounded crawler subsystem
├── lead-crawler                  # reconcile with Lead-Crawler; pick one canonical path later
├── prime-salesforce-dev          # bounded Salesforce subsystem
├── PPV_Website                   # bounded public web/app subsystem
├── azure-portal-dev              # bounded infra subsystem
└── salesforce-memory-agent       # supporting subsystem
```

---

## Important boundary rules

### Rule 1: this is not an immediate code merge
Do not collapse all repos into `PPV-Operator-OS`.

Interpretation of “move the stack over”:
- move ownership, planning, app-factory work, contracts, and operator surfaces to `PPV-Operator-OS`
- keep subsystem internals in their own repos unless a later decision explicitly merges them

### Rule 2: Operator-OS acts across subsystems through contracts
`PPV-Operator-OS` may:
- read subsystem health/status
- trigger bounded jobs
- coordinate workflows
- host the operator UI and autonomous loop logic

It should do so via:
- APIs
- CLI wrappers
- jobs/runners
- explicit adapters
- documented contract files

### Rule 3: subsystem repos keep internal ownership
- crawler logic stays in crawler repo
- Salesforce domain logic stays in Salesforce repo
- website logic stays in website repo
- infra logic stays in infra repo

### Rule 4: app-factory ownership leaves Lead-Crawler and Salesforce
Any docs that still imply the app factory “belongs” to `Lead-Crawler` or Salesforce should be rewritten to say:
- current subsystem integration lives there if needed
- canonical ownership now lives in `PPV-Operator-OS`

---

## Migration phases

### Phase 0: naming and authority lock
Objective: freeze the new ownership model before any implementation migration.

Deliverables:
- explicit approval that `PPV-Operator-OS` is the canonical operator/control-plane repo
- explicit approval that `PPV-Dev-Root` remains separate governance host
- explicit approval that subsystem repos remain bounded, not immediately merged

Verification:
- this plan doc exists
- user approval recorded in follow-up docs/commits

### Phase 1: create the repo and its canonical docs
Objective: create `PPV-Operator-OS` as a real repo with clear architectural ownership.

Create:
- `/home/eao168/PPV-Operator-OS/`
- `README.md`
- `AGENTS.md`
- `Architect-Docs/Operator-OS/*`
- `contracts/`, `apps/`, `services/`, `adapters/`

Initial docs must state:
- Operator-OS is the canonical app-factory/control-plane home
- PPV-Dev-Root remains outside it as governance/session-loader
- Lead-Crawler, Salesforce, Website, Azure remain bounded managed subsystems

Verification:
- repo exists and is git-initialized
- docs consistently describe the new ownership model

### Phase 2: contract-first realignment
Objective: define how Operator-OS talks to each subsystem before moving UI/workflows.

Create first-class contracts for:
- Lead-Crawler status/trigger contract
- Salesforce AI/status/read contract
- Website/event/intake visibility contract
- Azure infra/status/deploy support contract

Outputs:
- contract docs in `PPV-Operator-OS/contracts/`
- adapter placeholders in `PPV-Operator-OS/adapters/`
- initial issues in `Architect-Docs/Operator-OS/03-ISSUE-REGISTRY.md`

Verification:
- each subsystem has named read/write boundaries
- no subsystem is treated as a bag of ad hoc file pokes

### Phase 3: move app-factory/operator docs upward
Objective: move canonical planning/handoffs for app-factory work out of subsystem repos.

Actions:
- rewrite Lead-Crawler app-factory docs as transitional/integration-facing
- remove wording that says Lead-Crawler is the canonical owner
- point future app-factory/operator work to `PPV-Operator-OS`
- do same for any Salesforce docs that still imply operator-surface ownership

Verification:
- Lead-Crawler docs say crawler repo is a subsystem, not app-factory owner
- Salesforce docs say bounded integration only
- Operator-OS docs become the canonical handoff path

### Phase 4: migrate operator-facing surfaces
Objective: move operator UI/control-plane surfaces into Operator-OS.

Likely first slice:
- read-only operator views that aggregate subsystem status
- app-factory shell/navigation
- current “Salesforce AI” and crawler-ops visibility surfaces as bounded integrations

Out of scope initially:
- deep subsystem rewrites
- broad write/mutation features without approved contracts
- merging all runtime logic into Operator-OS

Verification:
- operator surface lives in `PPV-Operator-OS`
- subsystem repos expose data/actions through approved adapters/contracts

### Phase 5: cleanup and de-ambiguation
Objective: remove confusing duplicate ownership signals.

Actions:
- choose canonical casing/path between `Lead-Crawler` and `lead-crawler`
- quarantine or deprecate provisional app-factory UI inside subsystem repos once parity exists
- update root maps/docs to route app-factory/operator tasks to `PPV-Operator-OS`

Verification:
- no active doc says app factory canonically belongs to Lead-Crawler or Salesforce
- workspace routing consistently sends operator/control-plane work to Operator-OS

---

## Immediate next actions

1. Create `/home/eao168/PPV-Operator-OS` as a git repo.
2. Add `README.md` explaining the new domain boundary.
3. Add repo-local `AGENTS.md` aligned to PPV governance.
4. Create `Architect-Docs/Operator-OS/` canonical doc set.
5. Add an initial architecture doc defining bounded subsystems:
   - Lead-Crawler
   - Salesforce
   - Website
   - Azure
   - supporting tooling
6. Add a first issue for workspace realignment and contract bootstrap.
7. Patch Lead-Crawler docs so they describe themselves as subsystem host, not app-factory owner.
8. Patch any Salesforce-side docs that still imply dashboard/app-factory ownership.
9. Decide whether `ads-ops-job` belongs under `PPV_Website`, `PPV-Operator-OS`, or remains standalone.
10. Decide the canonical path between `Lead-Crawler` and `lead-crawler`.

---

## Open decisions that still need explicit approval

1. Should `ads-ops-job` be folded under website/product surfaces, treated as an Operator-OS managed workload, or stay standalone?
2. Which of `Lead-Crawler` vs `lead-crawler` is the canonical repo/path?
3. Should `PPV-Operator-OS` start as docs/contracts only, or should we immediately scaffold the first operator app/service folders?
4. Which current dashboard surface should be the very first migration slice:
   - crawler operations visibility
   - Salesforce AI visibility
   - unified operator home shell

---

## Recommended answer to the repo question

Yes: create `PPV-Operator-OS`.

No: do not merge the whole codebase into one repo right now.

Yes: realign the rest of the PPV product repos under its governance and control-plane ownership, while keeping `PPV-Dev-Root` separate as the governance/session-loader host.
