# Workspace AGENTS - PPV Dev Root

## Session Bootstrap (required every new session)
1) Read `GOVERNANCE.md`
2) Read `SESSION-RITUAL.md`
3) Read `CODEBASE-MAP.md`
4) Read `BUSINESS-CONTEXT.md`
5) Read the root contracts in `Dev-Loop-Contracts/`
6) After choosing a repository, read and follow its local `AGENTS.md`
7) If the task involves frontend, dashboards, admin tools, or operator UX, read `UI-DESIGN-PRINCIPLES.md`

## Workspace Layout (root)
- `PPV-Dev-Root/` = governance/session-loader host
- `PPV-Operator-OS/` = canonical operator/control-plane/app-factory repo
- `prime-salesforce-dev/` = Salesforce subsystem
- `PPV_Website/` = website/public-web subsystem
- `Lead-Crawler/` / `lead-crawler/` = crawler subsystem
- `azure-portal-dev/` = infra/orchestration subsystem
- `salesforce-memory-agent/` = supporting tooling subsystem

## Defaults
- review-only unless the user explicitly asks for edits or an active scoped task authorizes them
- reuse-first; search before creating
- respect frozen components and repo-local safety rules
- carry the business context from `BUSINESS-CONTEXT.md` into strategy, UX, lead-quality, ads, SEO, and automation work
- for internal UI work, follow `UI-DESIGN-PRINCIPLES.md`
- for internal operator-facing work, treat the UI as the human source of truth

## Root Role Model
This workspace inherits the root governance in `GOVERNANCE.md` and `Dev-Loop-Contracts/`:
- Hermes = controller / orchestrator / gate owner / human-facing narrator
- Codex = default implementation agent
- Claude = default independent reviewer
- User = final authority and escalation point

Repo-local docs must inherit this model unless a bounded temporary reassignment is explicitly recorded.

## Scoped Brief Rule
When Hermes expects another agent to act, Hermes should provide a copy-paste-ready scope block by default unless the user explicitly says not to.

Minimum required contents:
- gate or assignment statement
- repository and branch context
- exact task or issue/work-package ID if one exists
- objective
- allowed write scope
- explicit out-of-scope boundaries
- validation/acceptance requirements
- required handoff/doc updates if the repo or task has an active documentation workflow

## Task Documentation Rule
If a repo has an active canonical `Architect-Docs/<Stack>/` set, use it.
If not, use the repo's active local handoff/runbook path.
If no durable task-doc path exists, include scope, validation evidence, blockers, and next actions in the session handoff.

Do not assume stack-local docs can override root governance.
