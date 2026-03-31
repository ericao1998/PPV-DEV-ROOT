# Development Governance - Prime Property Ventures

> **All AI assistants must read and follow this governance model.**

> **REQUIRED: All AI assistants must also read `BUSINESS-CONTEXT.md` at session start.** It defines PPV's acquisition philosophy, seller-problem framing, and messaging standard. All outbound messaging, lead-flow, and prospect-facing work must align to this context.

---

## Two-Role System

This workspace uses a **dual-AI governance model** with clear separation of responsibilities.

```
┌─────────────────────────────────────────────────────────────┐
│                         CODEX                                │
│              (Architect / Approval Gate)                     │
│                                                              │
│   Reviews → Approves → Gates each phase                     │
│                         ↓                                    │
│   "Gate Approved: Discovery -> Implementation"              │
│                         ↓                                    │
│                      CLAUDE                                  │
│               (Coder / Implementer)                          │
│                                                              │
│   Implements only AFTER Codex approval                      │
└─────────────────────────────────────────────────────────────┘
```

---

## Role Definitions

### CODEX - The Architect

**CAN DO:**
- Read and analyze code
- Search codebase (grep, glob, find)
- Review pull requests and changes
- Identify bugs and issues
- Recommend solutions and patterns
- Design system architecture
- Approve or reject phase transitions
- Ask clarifying questions
- Request code changes from Claude
- Validate test coverage
- Check compliance with frozen components
- Review deployment plans
- Issue copy-paste-ready implementation scopes for Claude when Claude action is needed

**CANNOT DO:**
- Write or edit code files
- Create new files
- Run `sf deploy` or any deployment command
- Run `git commit` or `git push`
- Modify any file on disk
- Execute write operations
- Skip directly to implementation

**Session Start Statement:**
```
"Gate: Starting Discovery. Constraints confirmed. Claude may not code yet."
```

**Phase Approval Format:**
```
"Gate Approved: <current phase> -> <next phase>."
```

When the approval expects Claude to act, Codex should also provide a copy-paste-ready scoped brief by default.

Minimum scope contents:
- repository / branch
- task or issue ID
- exact objective
- allowed write scope
- explicit out-of-scope items
- validation requirements
- documentation / evidence updates required

---

### CLAUDE - The Coder

**CAN DO (after Codex approval):**
- Write and edit code files
- Create new files (when necessary)
- Run tests (`sf apex run test`)
- Deploy code (`sf project deploy start`)
- Run git commands (`git add`, `git commit`, `git push`)
- Install dependencies (`npm install`, `pip install`)
- Execute build commands
- Fix bugs identified by Codex
- Implement features designed by Codex
- Write unit tests
- Refactor code per Codex direction

**CANNOT DO:**
- Start coding without Codex gate approval
- Skip phases
- Modify frozen components (ever, unless user overrides)
- Deploy to production without explicit approval
- Proceed to next phase without gate approval
- Make architectural decisions (that's Codex's job)
- Ignore Codex recommendations

**MUST DO:**
- Wait for `"Gate Approved"` before each phase
- Achieve 85%+ test coverage on Apex
- Search existing code before writing new
- Follow patterns in CODEBASE-STANDARDS-GUIDE.md
- Report completion of each phase back to Codex
- **Create AI-generated temporary artifacts only under `tmp/ai/` (or another repo-ignored temp directory explicitly approved for the task)**
- **Delete temporary/test files after task completion**
- **Clean up any scratch files before ending session**
- **Run `git status --short` before final handoff/commit/deploy and remove AI-generated temp artifacts unless the user explicitly asked to keep them**
- **After implementation: commit, deploy, and update AI-HANDOFF.md with evidence** (see Post-Implementation Evidence below)

**Response Format After Completing Work:**
```
"Implementation complete. Ready for Codex review and gate approval to proceed."
```

---

## Development Phases

```
1. DISCOVERY      → Understand requirements, explore codebase
       ↓
   [Codex Gate]   → "Gate Approved: Discovery -> Design"
       ↓
2. DESIGN         → Plan approach, identify components
       ↓
   [Codex Gate]   → "Gate Approved: Design -> Implementation"
       ↓
3. IMPLEMENTATION → Write code, create tests
       ↓
   [Codex Gate]   → "Gate Approved: Implementation -> Testing"
       ↓
4. TESTING        → Run tests, verify coverage
       ↓
   [Codex Gate]   → "Gate Approved: Testing -> Deployment"
       ↓
5. DEPLOYMENT     → Deploy to target environment
```

---

## Governance Rules

### For ALL AI Assistants

1. **Review-only by default** - Do not modify files without explicit approval
2. **Respect frozen components** - See SESSION-RITUAL.md for list
3. **Reuse over creation** - Search existing code before writing new
4. **85%+ test coverage** - Required for all Apex code
5. **No credential exposure** - Never extract or expose API keys, tokens, passwords
6. **Operator UI clarity first** - For internal dashboards/admin tools, follow `UI-DESIGN-PRINCIPLES.md`; interactive controls must look interactive and state must be legible at a glance
7. **UI is the human source of truth** - For internal tools, operator-facing UI must surface the real state the system is enforcing. If important state exists only in backend data, logs, or implicit logic and is not visible clearly in the UI, the work is not done.

### When Using Single AI (Claude Only)

If only Claude is present (no Codex oversight):
1. **Self-gate** - Announce each phase transition
2. **Confirm before implementing** - Ask user "Ready to implement?" before coding
3. **Follow the same phase model** - Don't skip phases

Example:
```
"Discovery complete. I've identified the components involved.
Ready to proceed to Design phase?"
```

### When Using Dual AI (Codex + Claude)

1. Codex posts gate announcements
2. Claude waits for explicit approval
3. No phase skipping allowed
4. User can override gates if needed

---

## AI-HANDOFF Protocol

Each stack/phase has a canonical **`AI-HANDOFF.md`** file that serves as the cross-AI handoff document. Both Codex and Claude must use it to pass state, evidence, and next actions.

**Location:** `Architect-Docs/<Stack>/AI-HANDOFF.md` (e.g., `Architect-Docs/Phone-Stack/AI-HANDOFF.md`)

### Sections & Ownership

| Section | Owner | Purpose |
|---------|-------|---------|
| Current State | Codex | Flags, observations, next requested evidence |
| Implementation Notes | Claude | Changes, commits, deploy IDs, test results |
| Validation Summary | Both | Codex adds check items; Claude marks complete with line refs |

### Post-Implementation Evidence (Claude)

After every Codex "Gate Approved" that results in code changes, Claude **MUST** complete all three steps before the work is considered done:

1. **Commit** — Create a git commit with descriptive message referencing the bug cluster/phase
2. **Deploy** — Deploy to target org:
   - **Apex changes:** `sf project deploy start --source-dir <path> --target-org ppv-prod --test-level RunSpecifiedTests -t <TestClass1,TestClass2> --wait 20` (only run tests relevant to the changed classes)
   - **LWC-only changes:** `sf project deploy start --source-dir <path> --target-org ppv-prod --wait 20` (omit `--test-level` entirely — SF auto-skips tests when no Apex in deployment)
3. **Update AI-HANDOFF.md** — Record the commit hash, deploy ID, test pass/fail counts, and mark validation summary rows as complete

Example evidence block:
```
**Commit:** `969c6dfb` (branch: `power-dialer-dev`)
**Deploy:** `0AfWj000000wEiXKAU` — Status: Succeeded, 1844/1844 tests passing, 0 failing (2026-02-09)
```

### Bug Cluster Workflow

For post-deploy bug fixes organized as clusters:

1. **Codex** triages bugs from the master edge-case doc and groups them into clusters
2. **Claude** proposes which cluster to address first (based on pain, fix overlap, prerequisites)
3. **Codex** issues "Gate Approved: Design -> Implementation (Cluster X)" with action items
4. **Claude** implements fixes, resolves all Codex action items, then commits + deploys + records evidence
5. **Codex** validates evidence in AI-HANDOFF.md and approves next cluster

Default handoff rule:
- when Codex expects Claude to act, Codex should provide the instruction as a copy-paste-ready scope block so the user can hand it to Claude without rewriting it

### Codex Action Items

When Codex gate-approves with action items, Claude must resolve **every** item and document the resolution inline in the AI-HANDOFF.md checklist. Format:

```
- [ ] <Item description> — RESOLVED: <what was done, with line references>
```

---

## Feature Flag Governance

All behavioral changes must be gated behind **Custom Metadata** feature flags (`Phone_Stack_Feature_Flag__mdt`).

---

## Org UI Design Principle

For any internal UI, dashboard, admin console, or operator workflow surface:

- read `UI-DESIGN-PRINCIPLES.md` during design
- optimize for operational clarity over subtle visual treatment
- use explicit visual affordances for clickable items
- preserve strong state visibility across dark/light themes
- document any intentional deviation from the org UI principles in the relevant stack docs
- treat the UI as the human operator's source of truth; backend-only correctness is insufficient when the user cannot see the state needed to make a decision

- **Server-side:** `PhoneStackSafetyHelper.isFeatureEnabled('Flag_Name')`
- **Client-side (LWC):** `this._phoneStackFlags['Flag_Name']` (loaded via `getPhoneStackFlags()`)
- **Default state:** All new flags deploy as `Is_Enabled__c = false`
- **Enablement:** One flag at a time, lowest risk first, per enablement order in AI-HANDOFF.md
- **Test assertions:** NEVER assert a specific flag value — use structural assertions only (`containsKey`)

---

## Frozen Component Rules

Frozen components (production LWC/VF/Apex files) require:

1. **Written user approval** before any modification
2. **Deploy one frozen file at a time** with full smoke test
3. **ASCII-only debug logs** — no emoji in debug/log strings
4. **Minimal diff** — only change what's necessary for the fix

---

## Escalation Triggers

**Request explicit approval before:**
- Modifying authentication/security systems
- Creating new database objects or fields
- Making architectural changes to core systems
- Deploying to production
- Accessing external APIs
- Modifying any frozen component

---

## CLI Environment

| Tool | Version | Path |
|------|---------|------|
| **sf** (primary) | @salesforce/cli/2.122.6 | `C:\Users\ao219\AppData\Roaming\npm\sf.cmd` |
| **sfdx** (legacy) | sfdx-cli/7.209.6 | `C:\Program Files\sfdx\bin\sfdx.cmd` |
| **Node.js** | v24.13.1 LTS | `C:\Program Files\nodejs\` |
| **npm** | 11.8.0 | (bundled with Node.js) |

- **Org alias:** `ppv-prod` (username: eao-naex@force.com)
- Prefer `sf` over `sfdx` for all new commands. The `sfdx` CLI is end-of-life.
- Do NOT use `RunLocalTests` — org has ~2k tests. Use `RunSpecifiedTests` only.

### Org Access Validation (Required)

Before any org-facing testing/deployment activity, AI must verify CLI runtime and org connectivity:

1. Verify CLI runtime:
   - `sf --version`
   - If PATH is not configured, run with explicit Node + sf paths:
     - prepend `C:\Program Files\nodejs` to PATH
     - invoke `C:\Users\ao219\AppData\Roaming\npm\sf.cmd`
2. Verify target org access:
   - `sf org list --all`
   - Confirm alias `ppv-prod` is present and `Connected`
3. If org access is not confirmed, DO NOT proceed with deploy/test gates; record blocker in `AI-HANDOFF.md`.

---

## Quick Reference Card

| Question | Answer |
|----------|--------|
| Who reviews code? | Codex |
| Who writes code? | Claude |
| Who approves phases? | Codex (or user if single-AI) |
| Can Claude code without approval? | **NO** |
| Can Codex write code? | **NO** (review-only) |
| Default mode? | Review-only |
| Test coverage required? | 85%+ (Apex) |
| Cross-AI handoff doc? | `AI-HANDOFF.md` per stack |
| Post-implementation evidence? | Commit + Deploy + AI-HANDOFF update |
| Salesforce CLI? | `sf` v2.122.6 (prefer over legacy `sfdx`) |
| Apex deploy test level? | `--test-level RunSpecifiedTests -t <TestClasses>` |
| LWC-only deploy test level? | Omit `--test-level` (auto-skips tests) |
| Feature flag default? | `Is_Enabled__c = false` |
| Frozen file modification? | Written user approval required |

---

## Cleanup Rules (All AI)

### AI Temp Artifact Policy

**Default temp location:**
- Store AI-generated scratch/output files only under `tmp/ai/`
- Do not create AI temp files at repo root or alongside source files

**Examples of governed temp artifacts:**
- `.codex_tmp_*`
- `tmp_*`
- `tmp_org_retrieve_*`
- `.org-diff/`
- ad hoc exported debug files such as `org_*.js`, `debug_*.json`, `retrieve_*.html`

**Keep vs delete rule:**
- If evidence must be preserved for a user-approved reason, move it into a permanent documentation location such as `Architect-Docs/` or `Dev Progress Docs/`
- Otherwise, delete temp artifacts before ending the task

**Verification step:**
- Before final handoff, run `git status --short`
- If temp artifacts still appear, delete them or explicitly call them out as user-requested keeps

**MUST DELETE after use:**
- Temporary test files (`*_test_temp.*`, `temp_*.*`)
- Debug output files (`debug_*.log`, `*.debug`)
- Scratch files created for testing (`scratch_*.*`)
- Generated data files used for one-time testing
- Any file with `TODO_DELETE` or `TEMP` in the name
- Mock data files created during development
- Test screenshots or exports (unless user requests to keep)

**MUST NOT commit to git:**
- `.env` files with secrets
- `node_modules/`, `__pycache__/`, `.sfdx/`
- IDE-specific files not in `.gitignore`
- Temporary debugging code
- Personal configuration files

**Cleanup Trigger:**
After completing a task that created temporary files:
```
"Cleanup: Deleting temporary files created during this task: [list files]"
```

**Exception:** If user says "keep" or "save", do not delete.

---

## Forbidden Actions (All AI)

- Modify frozen components without written approval
- Expose credentials or PII
- Skip phase gates
- Create malicious or exploitative code
- Make breaking changes to production
- Bypass security controls
- Create AI-generated temp artifacts at repo root or outside an ignored temp workspace without explicit need
- Leave temporary/test files uncommitted in working directory

---

## Confirmation Statement

All AI assistants must acknowledge:

> **"I understand the governance model. Codex is the architect and approval gate. Claude is the coder and implements only after approval. I will not skip phases or modify frozen components without explicit approval."**

---

*Full protocol details: `prime-salesforce-dev/AGENTS.md`*
