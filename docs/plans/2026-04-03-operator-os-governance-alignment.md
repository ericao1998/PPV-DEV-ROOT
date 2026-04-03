# PPV Governance Alignment Plan for PPV-Operator-OS and the Hermes Autonomous Loop

> For Hermes: execute this as a contract-first normalization across workspace-root docs, repo-local AGENTS/docs, and any live loop wiring. Do not patch runtime scripts until the role model is explicit and approved everywhere.

Goal: eliminate governance drift across the PPV workspace so every repo aligns to the same autonomous operating model: `PPV-Dev-Root` remains the governance/session-loader host, `PPV-Operator-OS` becomes the canonical operator/control-plane repo, Hermes is the controller/front door, and Codex/Claude act through explicit bounded roles instead of repo-by-repo improvisation.

Architecture: this is a two-layer contract migration. Root contracts in `PPV-Dev-Root` must define the non-overridable process law for the autonomous loop, while each repo keeps only local architecture/runbook/handoff rules. Existing docs currently mix at least three role models (Codex-gated dual AI, Hermes-controller tri-agent, and older local migration rules), so the first task is to normalize the authority model before more runtime or repo realignment work continues.

Tech stack: markdown governance docs, repo-local `AGENTS.md`, `Architect-Docs/*`, autonomous-loop wiring docs/scripts, git.

---

## Executive finding

The workspace is currently drifted.

The drift is structural, not cosmetic:
- root governance still presents Codex/Claude dual-AI as the default authority model
- some stack docs correctly show Hermes as controller
- some recent ILA docs were rewritten back to a Codex-controller model
- older migration docs still present Codex as architect/gatekeeper and Claude as coder
- several repo-local `AGENTS.md` files still instruct: "When Codex determines that Claude should execute..."

This means the same workspace can currently tell an agent all of the following, depending on which file it reads first:
- Codex is the gate and Claude is the coder
- Hermes is the gate, Codex implements, Claude reviews
- Codex is the controller and Hermes/Claude are delegated workers

That is a broken operating contract.

---

## Concrete drift found

### Root workspace drift (`PPV-Dev-Root`)

#### `/home/eao168/PPV-Dev-Root/GOVERNANCE.md`
Drift:
- still centers dual-AI Codex/Claude governance as the default workspace law
- includes a Hermes-controller section, but only as a stack-specific override
- also contains contradictory sections about handoff ownership and `AI-HANDOFF.md` decommissioning even though active stacks still use canonical `AI-HANDOFF.md`

Specific evidence:
- lines 181-195: Hermes-controller tri-agent model appears as a stack-local special case
- lines 203-205: says legacy `Architect-Docs/<Stack>/AI-HANDOFF.md` is decommissioned
- active stacks still use `AI-HANDOFF.md` as canonical state/evidence files

#### `/home/eao168/PPV-Dev-Root/SESSION-RITUAL.md`
Drift:
- session bootstrap still tells the operator to understand Codex/Claude roles
- role confirmation is still phrased as `I am [Codex/Claude]`
- handoff rule still assumes Codex hands implementation to Claude

Specific evidence:
- lines 267, 271, 292, 301-303

#### `/home/eao168/PPV-Dev-Root/AGENTS.md`
Drift:
- contains Codex->Claude scoped-handoff rule as if that is still global workspace governance

Specific evidence:
- line 33

### ILA stack drift (`prime-salesforce-dev`)

#### `/home/eao168/prime-salesforce-dev/Architect-Docs/Inbound-Lead-Automation/AI-HANDOFF.md`
Mixed state:
- header says Hermes controller, Codex implementation, Claude review
- but body still references Lead-Crawler as app-factory target instead of the newly approved `PPV-Operator-OS`
- same file contains historical notes that refer to a Codex-controller operating model and `SYSTEM-WIRING.md` as Codex-controller wiring

Specific evidence:
- lines 4, 18-23 = Hermes-controller role model
- lines 203-210 = still describing a Codex-controller operating model and Codex as Slack-visible coordinator

#### `/home/eao168/prime-salesforce-dev/Architect-Docs/Inbound-Lead-Automation/04-ACTIVE-HANDOFF.md`
Drift:
- current-state header says Hermes controller / Codex implementer / Claude reviewer
- later checkpoint records a 2026-04-02 refactor to Codex-controller loop

Specific evidence:
- line 13 = Hermes controller
- lines 257-265 = Codex-controller refactor checkpoint

This is one of the clearest signs that the stack docs were patched in opposite directions across sessions.

### Lead-Crawler drift

#### `/home/eao168/Lead-Crawler/Architect-Docs/Lead-Crawler/AI-HANDOFF.md`
Drift:
- current owners line still says `Codex, User`
- does not match the desired Hermes-led autonomous loop

Specific evidence:
- line 4

#### historical migration docs under `/home/eao168/Lead-Crawler/doc/local-migration/`
Drift:
- repeatedly encode the old Codex architect/gatekeeper / Claude coder model
- these should be archived as historical, not left as ambiguous operating guidance

### PPV_Website drift

#### `/home/eao168/PPV_Website/Architect-Docs/Google-Ads-Realignment/AI-HANDOFF.md`
Drift:
- owners still read `Codex (state and gates), Claude (implementation...)`
- issue table repeatedly encodes Codex design/governance ownership and Claude implementation only

Specific evidence:
- line 4 and many planning rows throughout

### `azure-portal-dev` and other repo-local AGENTS drift

#### `/home/eao168/azure-portal-dev/AGENTS.md`
Drift:
- still says: when Codex determines Claude should execute implementation work...

#### `/home/eao168/PPV_Website/AGENTS.md`
Drift:
- same Codex->Claude handoff assumptions, plus older repo nesting assumptions

#### `/home/eao168/salesforce-memory-agent/AGENTS.md`
Drift:
- same Codex->Claude scoped-handoff assumptions

---

## Approved target model to normalize toward

This is the recommended canonical model based on current direction and recent approvals.

### Root authority
- `PPV-Dev-Root` is the governance/session-loader host
- `PPV-Operator-OS` is the canonical operator/control-plane/app-factory repo

### Autonomous loop role model
- Hermes = controller / orchestrator / front door / gate owner / Slack-visible status narrator
- Codex = primary implementation agent unless a local stack contract explicitly assigns a bounded architecture task instead
- Claude = independent reviewer / critique / verification agent by default
- User = final authority, approval override, escalation point, pin-architect/human interrupt authority

### Important nuance
This does not mean Codex can never review or Claude can never implement.
It means:
- the default role model must be explicit and consistent
- any temporary role shift must be documented as a bounded exception
- controller replacement must require explicit user approval

### Slack contract
- Slack is human-facing briefing/status surface, not raw machine-log sink
- Hermes owns the human-facing thread narrative
- Codex and Claude contribute evidence/results through the controller unless an explicit local exception exists
- ping user only for real decisions/escalations

### Repo governance model
- root contracts define non-overridable process law
- repo-local docs define only local architecture, runbooks, issues, handoff, and bounded runtime details
- no repo may silently redefine the controller or global role model

---

## Required two-layer contract structure

## Layer 1: root contracts in `PPV-Dev-Root`

Create or normalize a compact root contract package, for example:

```text
PPV-Dev-Root/
└── Dev-Loop-Contracts/
    ├── 00-START-HERE.md
    ├── 01-OPERATING-MODEL.md
    ├── 02-STATE-AND-SCHEMAS.md
    ├── 03-HUMAN-UX-AND-GOVERNANCE.md
    └── 04-REPO-INHERITANCE-AND-EXCEPTIONS.md
```

These files should define the non-overridable rules for:
- controller identity and authority
- default Codex / Claude roles
- user override and escalation authority
- phase / condition / human-status vocabulary
- Slack contract
- pin-architect behavior
- temporary role-shift rules
- repo inheritance rules
- requirement that stack docs may not redefine the global controller

## Layer 2: stack-local contracts in each repo
Each repo keeps:
- architecture
- issue registry
- active handoff
- AI handoff
- process runbook
- local AGENTS rules

But those docs must inherit the root role model and must not restate contradictory global process law.

---

## Required repo-by-repo alignment actions

### A. `PPV-Dev-Root`

Objective: make the root governance unambiguously Hermes-led and repo-inheritable.

Actions:
1. Rewrite `GOVERNANCE.md`
   - remove Codex/Claude dual-AI as the default global law
   - replace with root autonomous-loop contract summary
   - preserve any single-agent fallback rules as degraded-mode appendix only
2. Rewrite `SESSION-RITUAL.md`
   - bootstrap must ask which repo/stack is in focus
   - role confirmation must include Hermes/Codex/Claude model, not just Codex/Claude
   - remove Codex->Claude default handoff wording
3. Patch `AGENTS.md`
   - replace Codex->Claude handoff block with controller-owned scoped-brief rule
4. Add `Dev-Loop-Contracts/` root contract set
5. Update `CODEBASE-MAP.md`
   - route operator/control-plane/app-factory work to `PPV-Operator-OS`
   - mark old assumptions as superseded

### B. `PPV-Operator-OS` (new repo)

Objective: become the canonical product/operator/control-plane owner.

Actions:
1. create repo-local `AGENTS.md` that explicitly inherits root contracts
2. create canonical `Architect-Docs/Operator-OS/`
3. make this the default home for:
   - app factory
   - operator UI
   - control-plane contracts
   - cross-subsystem orchestration
4. define subsystem contract folders:
   - Lead-Crawler
   - Salesforce
   - Website
   - Azure

### C. `prime-salesforce-dev`

Objective: keep Salesforce as bounded subsystem and clean up ILA role drift.

Actions:
1. patch `Inbound-Lead-Automation/AI-HANDOFF.md`
   - keep one approved role model only
   - remove/supersede Codex-controller narrative
   - update app-factory target from Lead-Crawler to `PPV-Operator-OS`
2. patch `04-ACTIVE-HANDOFF.md`
   - mark Codex-controller checkpoint as superseded by the root contract model
3. patch any repo-local AGENTS docs to controller-owned scoped-brief language
4. ensure ILA stack docs say:
   - Hermes = controller
   - Salesforce = subsystem
   - dashboard/app-factory is not owned here

### D. `Lead-Crawler`

Objective: keep crawler repo as subsystem, not app-factory owner, and remove stale Codex-first governance.

Actions:
1. patch `Architect-Docs/Lead-Crawler/AI-HANDOFF.md` owner line
2. add explicit note that historical local-migration docs are archival and superseded for governance
3. ensure active docs route operator/control-plane work upward to `PPV-Operator-OS`

### E. `PPV_Website`

Objective: normalize current ads/site stack docs to the Hermes-led contract.

Actions:
1. patch `AI-HANDOFF.md` owner lines and role wording
2. replace Codex-as-global-gate language with controller-owned gate language
3. keep any stack-specific design authority language only if explicitly local and non-conflicting
4. align AGENTS doc to the root controller model

### F. `azure-portal-dev` and `salesforce-memory-agent`

Objective: remove stale Codex->Claude handoff assumptions from AGENTS docs.

Actions:
1. patch local `AGENTS.md`
2. state inheritance from root contracts
3. restrict repo-local docs to local infra/tooling specifics

---

## Migration order

### Phase 1: lock root contracts
Deliverable:
- root contract package exists
- root `GOVERNANCE.md`, `SESSION-RITUAL.md`, and `AGENTS.md` no longer contradict Hermes-led control

### Phase 2: create `PPV-Operator-OS`
Deliverable:
- repo exists
- canonical docs exist
- subsystem model documented there

### Phase 3: patch repo-local governance docs
Deliverable:
- all `AGENTS.md` files inherit the same controller model
- active `AI-HANDOFF.md` / `ACTIVE-HANDOFF.md` files no longer contradict root contracts

### Phase 4: patch runtime/loop wiring docs and scripts
Deliverable:
- any `SYSTEM-WIRING.md`, worker wrappers, doctor scripts, or Slack routing logic use the same role model the docs describe
- no live runtime still thinks Codex is controller if the docs say Hermes is controller

### Phase 5: archive/supersede stale governance artifacts
Deliverable:
- old local-migration and pre-normalization docs are explicitly marked historical
- future readers cannot mistake them for active operating law

---

## Validation checklist

A repo is aligned only when all of the following are true:
- its `AGENTS.md` no longer encodes stale Codex-first handoff assumptions
- its active handoff/AI-handoff docs match the root role model
- it does not claim app-factory ownership if that now belongs to `PPV-Operator-OS`
- if it has autonomous-loop scripts/docs, those scripts/docs agree on controller identity
- Slack human-facing narrative rules are consistent with root governance

Workspace-wide validation pass:
1. search all PPV repos for `When Codex determines that Claude should execute`
2. search all PPV repos for `Codex-controller`
3. search all PPV repos for owner lines in `AI-HANDOFF.md`
4. search all PPV repos for `Lead-Crawler` as app-factory owner after `PPV-Operator-OS` creation
5. verify root docs now clearly state repo inheritance rules

---

## Practical next implementation slice

Recommended next live work package:
1. patch root `PPV-Dev-Root` governance docs first
2. create `PPV-Operator-OS` repo and root docs
3. patch repo-local `AGENTS.md` files in:
   - `PPV_Website`
   - `azure-portal-dev`
   - `salesforce-memory-agent`
   - `prime-salesforce-dev`
   - `Lead-Crawler`
4. patch the active stack docs with the highest contradiction risk:
   - `prime-salesforce-dev/Architect-Docs/Inbound-Lead-Automation/AI-HANDOFF.md`
   - `prime-salesforce-dev/Architect-Docs/Inbound-Lead-Automation/04-ACTIVE-HANDOFF.md`
   - `Lead-Crawler/Architect-Docs/Lead-Crawler/AI-HANDOFF.md`
   - `PPV_Website/Architect-Docs/Google-Ads-Realignment/AI-HANDOFF.md`
5. only after doc normalization, patch any live loop runtime wiring that still assumes the old controller

---

## Bottom line

Yes: the workspace docs are drifted.

The fix is not one more local patch.
The fix is:
- root contract normalization in `PPV-Dev-Root`
- creation of `PPV-Operator-OS` as canonical operator/control-plane repo
- repo-local inheritance of the Hermes-led role model
- explicit supersession of Codex-first governance where it is still active
- runtime/script alignment only after the governance contract is unified
