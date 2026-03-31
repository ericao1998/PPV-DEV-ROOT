# Workspace AGENTS - PPV Dev Root

## Session Bootstrap (required every new session)
1) Read CODEBASE-MAP.md
2) Read GOVERNANCE.md
3) Read SESSION-RITUAL.md
4) Read BUSINESS-CONTEXT.md
5) Apply the WSL/Windows Git interop rule from SESSION-RITUAL.md before any auth-sensitive Git command
6) If the task involves frontend, dashboards, admin tools, or operator UX, read `UI-DESIGN-PRINCIPLES.md`

## Workspace Layout (root)
- prime-salesforce-dev/ (Salesforce DX repo)
- azure-portal-dev/ (Azure portal dev repo)
- salesforce-memory-agent/ (VS Code extension repo)

## Per-Repo Instructions
After choosing a repository, read and follow its local AGENTS.md (if present).

## Defaults
- review-only unless the user explicitly asks for edits
- reuse-first; search before creating
- respect frozen components
- carry the business context from `BUSINESS-CONTEXT.md` into all strategy, UX, lead-quality, ads, SEO, and automation work
- when running in `bash` under `/mnt/c/...`, prefer `cmd.exe /c "git -C C:\\... <command>"` for auth-sensitive Git commands if the repo uses HTTPS + Windows Git Credential Manager
- in this workspace's `bash` environment, prefer `python3` instead of `python`; `node` and `npm` are available natively; `az` is available through the Windows-installed CLI path
- for internal UI work, follow `UI-DESIGN-PRINCIPLES.md`; do not ship ambiguous clickable elements or low-clarity operator controls
- for internal operator-facing work, treat the UI as the human source of truth; backend-only correctness is not sufficient if the user cannot see the state they need

## Claude Handoff Scope Rule

When Codex determines that Claude should execute implementation work, Codex must provide a copy-paste-ready scope block by default unless the user explicitly says not to.

Minimum required contents:
- gate statement
- repository and branch context
- exact task or issue/work-package ID if one exists
- allowed write scope
- explicit out-of-scope boundaries
- validation/acceptance requirements
- required handoff/doc updates if the stack uses canonical `Architect-Docs/`

This rule is intended to remove repeated user prompting for "copy and paste scope" and should be treated as the default handoff style in this workspace.

## Stack Documentation Ground Rule

Any stack-level development task that may span more than one session, more than one AI turn, or more than one implementation phase must use a canonical `Architect-Docs/<Stack>/` document set.

Required file contract for each stack:
- `00-START-HERE.md`
- `01-ARCHITECTURE.md`
- `02-RUNBOOKS.md`
- `03-ISSUE-REGISTRY.md`
- `04-ACTIVE-HANDOFF.md`
- `AI-HANDOFF.md`
- `PROCESS-RUNBOOK.md`
- `DOCS-INDEX.md`
- `archive/`
- `templates/`

Required operating rules:
- No implementation begins until the issue is logged in `03-ISSUE-REGISTRY.md`
- Current scope and next actions must be recorded in `04-ACTIVE-HANDOFF.md`
- `AI-HANDOFF.md` is the only active cross-AI state file for that stack
- Stable system design belongs in `01-ARCHITECTURE.md`, not in handoff files
- Session-end checkpoints must be recorded for any non-trivial stack task
- Stale notes must be archived instead of expanding the active handoff indefinitely

File responsibilities:
- `04-ACTIVE-HANDOFF.md` = current rolling work log for the active lane or sprint; update this every session that touches the stack
- `AI-HANDOFF.md` = canonical cross-AI state, gates, work queue, and evidence ledger; update this whenever gate state, implementation evidence, validation evidence, or deployment evidence changes

Mandatory session-close rule:
- Before ending any stack session, append a checkpoint to `04-ACTIVE-HANDOFF.md`
- If the session produced implementation, validation, deployment, or gate evidence, also update `AI-HANDOFF.md`

Required checkpoint format:
```md
Checkpoint YYYY-MM-DD:
- Scope:
- Changed:
- Validated:
- Blocked:
- Next:
```

If a stack does not yet have this structure, creating or normalizing the stack doc set is the first planning task before implementation begins.
