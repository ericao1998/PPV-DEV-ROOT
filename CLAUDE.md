# PPV Workspace Root - Mandatory Session Rules

> **CRITICAL: Read these files BEFORE any work. They are binding governance.**

## Mandatory Reading (Every Session)

1. **`GOVERNANCE.md`** — Codex/Claude roles, phase gates, post-implementation evidence
2. **`SESSION-RITUAL.md`** — Frozen components, domain routing, session start checklist
3. **`CODEBASE-MAP.md`** — Domain routing, component dependencies, reusable patterns
4. **`BUSINESS-CONTEXT.md`** — PPV acquisition philosophy, seller-problem framing, messaging standard. All outbound messaging and lead-flow work must align to this context.

## Deployment Rules (Non-Negotiable)

- **Apex changes:** `sf project deploy start --source-dir <path> --target-org ppv-prod --test-level RunSpecifiedTests -t <TestClass1,TestClass2> --wait 20`
- **LWC-only changes:** `sf project deploy start --source-dir <path> --target-org ppv-prod --wait 20` (omit `--test-level`)
- **Metadata-only (Custom Metadata, objects, fields):** Deploy separately first, then Apex
- **NEVER** use `RunLocalTests` — it runs ALL 1900+ org tests. Always use `RunSpecifiedTests` with targeted test classes.
- **Deploy from:** `c:\Dev\prime-salesforce-dev\` (primary working repo). Do NOT deploy from OneDrive.

## Role Acknowledgment

At session start, state:
> "I am Claude (Coder). I will follow GOVERNANCE.md, respect frozen components, and use RunSpecifiedTests for Apex deploys."

## Closeout Report Format (Non-Negotiable)

- All closeout reports for Codex review **MUST** be wrapped in a fenced code block (triple backticks) so they can be copied as one unit.
- Do NOT use inline markdown in conversation flow for closeouts — Codex needs copy-paste-ready plain text.
- Every closeout must include: files changed, behavior summary, test results, deploy ID (if applicable), and any blockers.

## Repo-Specific Rules

Each sub-repo has its own CLAUDE.md with additional context:
- `prime-salesforce-dev/CLAUDE.md` — Salesforce deployment, frozen components, Phone VF guidelines
- `azure-portal-dev/CLAUDE.md` — Lead crawler pipeline rules, Azure infra
