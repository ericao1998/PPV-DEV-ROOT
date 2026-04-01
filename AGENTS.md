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
- PPV_Website/ (website and adjacent web/app repo)
- lead-crawler/ (crawler and data-pipeline repo)
- azure-portal-dev/ (shared Azure infra/orchestration repo)
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
- required handoff/doc updates if the repo or task has an active documentation workflow

This rule is intended to remove repeated user prompting for "copy and paste scope" and should be treated as the default handoff style in this workspace.

## Task Documentation Rule

The legacy `Architect-Docs/<Stack>/` workflow has been decommissioned and is not a default requirement.

Use the current repo-local docs, user-specified handoff files, CI/governance files, or other active documentation paths when a task requires persistent notes or evidence. If no active doc path exists, include scope, validation evidence, blockers, and next actions in the session handoff.
