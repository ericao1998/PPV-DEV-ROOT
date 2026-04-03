# Session Ritual - Prime Property Ventures (PPV)

Read this first. This document orients any AI assistant to the workspace structure, active work, and governance rules.

## Business Context Bootstrap
All AI sessions must also read `BUSINESS-CONTEXT.md`.

That document is the root business brief for this workspace and exists to keep all sessions aligned on PPV's real objective:
- acquire property below market by solving seller problems
- identify seller problems, constraints, and fit before proposing messaging, targeting, automation, or UX changes
- optimize for qualified seller opportunities, not shallow traffic or lead volume

If a session ignores seller-problem fit, it is likely optimizing the wrong thing.

---

## Workspace Topology

Top-level roles:
- `PPV-Dev-Root` = governance/session-loader host
- `PPV-Operator-OS` = canonical operator/control-plane/app-factory repo

Bounded subsystem repos:
- `prime-salesforce-dev` = Salesforce subsystem
- `PPV_Website` = website/public-web subsystem
- `Lead-Crawler` / `lead-crawler` = crawler subsystem
- `azure-portal-dev` = infra/orchestration subsystem
- `salesforce-memory-agent` = supporting tooling subsystem

Operator/control-plane/app-factory work should route to `PPV-Operator-OS` unless a repo-local contract explicitly says the task is bounded subsystem work.

---

## Governance Summary

Full governance lives in:
- `GOVERNANCE.md`
- `Dev-Loop-Contracts/`

Default root role model:
- Hermes = controller / orchestrator / gate owner / Slack-visible narrator
- Codex = default implementation agent
- Claude = default independent reviewer
- User = final authority / escalation / override

Temporary role shifts are allowed only when they are explicitly recorded for the bounded task/lane.
Controller replacement requires explicit user approval.

---

## Working Rules

1. Review-only by default
2. Reuse-first
3. Respect frozen components and repo-local safety rules
4. Keep operator-facing UI as the human source of truth
5. Do not expose secrets or sensitive data
6. Keep temp AI artifacts in repo-ignored temp paths such as `tmp/ai/`
7. Route app-factory/operator/control-plane ownership to `PPV-Operator-OS`
8. Do not let stack-local docs silently override root governance

---

## WSL / Windows Git Interop

When the session is running in `bash` under `/mnt/c/...`, Git auth can fail if the repo uses an HTTPS remote and the configured credential helper points to Windows Git Credential Manager.

Bootstrap check before auth-sensitive Git commands:
```bash
pwd
git remote -v
git config --get credential.helper
```

If all of the following are true:
- current shell is `bash`
- working tree is under `/mnt/c/...`
- Git remote is `https://github.com/...`
- credential helper resolves to Windows Git Credential Manager

Then:
- use normal `git` in `bash` for read-only commands
- use `cmd.exe /c "git -C C:\\... <command>"` for auth-sensitive Git commands

---

## Runtime Command Map

Bootstrap check near the start of the session:
```bash
command -v python || true
command -v python3 || true
command -v node || true
command -v npm || true
command -v az || true
```

Known good defaults for this workspace:
- prefer `python3` over `python` in `bash`
- `node` and `npm` are available natively
- `az` may come from the Windows-installed CLI path

---

## Session Start Checklist

1. [ ] Read this `SESSION-RITUAL.md`
2. [ ] Read `GOVERNANCE.md`
3. [ ] Read `CODEBASE-MAP.md`
4. [ ] Read `BUSINESS-CONTEXT.md`
5. [ ] Read the root contracts in `Dev-Loop-Contracts/`
6. [ ] Read the focused repo's local `AGENTS.md`
7. [ ] Acknowledge frozen components cannot be modified
8. [ ] Confirm your active role in the current task, using the Hermes/Codex/Claude model
9. [ ] Confirm which repository/branch is in scope
10. [ ] Ask whether the task is bounded subsystem work or operator/control-plane work
11. [ ] Read `CICD.md` in the focused repo before deployment work
12. [ ] Run the WSL/Windows Git interop bootstrap check before auth-sensitive Git work
13. [ ] Run the runtime command bootstrap check and normalize command names
14. [ ] If Hermes is assigning work to another agent, produce a copy-paste-ready scoped brief by default unless the user says not to

## Auto-Confirm Pattern

Use a confirmation like:
- Gate: Starting Discovery. Constraints confirmed.
- I understand the root governance model: Hermes is controller, Codex is the default implementation agent, Claude is the default independent reviewer, and the user is final authority.
- I will prioritize reuse over creation, respect frozen components, and keep operator-facing state truthful in the UI.

---

## Quick Commands Reference

```bash
# Check current branch
git branch --show-current

# WSL/Windows Git auth-safe push pattern
cmd.exe /c "git -C C:\Dev\PPV_Website push origin PPV-Website"

# Python in this workspace
python3 --version
python3 script.py

# Node/npm
node --version
npm --version
```
