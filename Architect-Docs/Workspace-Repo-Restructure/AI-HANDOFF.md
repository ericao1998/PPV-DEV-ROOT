# AI-HANDOFF - Workspace Repo Restructure

Last Updated: 2026-04-03
Owners: Hermes (controller/state/gates), Codex (implementation), Claude (review/evidence)

## Current State
- Phase: completed workspace realignment baseline
- Goal achieved: `PPV-Dev-Root` is now the governance/session-loader host, while `PPV-Operator-OS` is the canonical operator/control-plane/app-factory repo
- Subsystem repos are now treated as bounded domains rather than canonical app-factory owners:
  - `Lead-Crawler`
  - `prime-salesforce-dev`
  - `PPV_Website`
  - `azure-portal-dev`
  - `salesforce-memory-agent`

## Validation Summary

| Item | Status | Evidence |
|---|---|---|
| Root governance normalized to Hermes-led model | Complete | `AGENTS.md`, `GOVERNANCE.md`, `SESSION-RITUAL.md`, `Dev-Loop-Contracts/` |
| Workspace routing points operator/control-plane ownership to `PPV-Operator-OS` | Complete | `AGENTS.md`, `SESSION-RITUAL.md`, `docs/plans/2026-04-03-ppv-operator-os-workspace-realignment.md` |
| `PPV-Operator-OS` canonical repo scaffold created | Complete | `/home/eao168/PPV-Operator-OS/{README.md,AGENTS.md,Architect-Docs/,contracts/,apps/,services/,adapters/}` |
| Repo-local AGENTS inheritance updated across bounded subsystems | Complete | `Lead-Crawler/AGENTS.md`, `PPV_Website/AGENTS.md`, `azure-portal-dev/AGENTS.md`, `salesforce-memory-agent/AGENTS.md`, `prime-salesforce-dev/AGENTS.md` |
| Active subsystem docs no longer present Lead-Crawler or Salesforce as canonical app-factory owner | Complete | Lead-Crawler and Salesforce stack docs updated on 2026-04-03 |

## Open Items
- [ ] Add/confirm git remote for `PPV-Operator-OS` before push-based workflow can be considered fully closed
- [ ] Choose the first implemented Operator-OS contract-backed UI slice
- [ ] Decide canonical casing/path between `Lead-Crawler` and `lead-crawler`
- [ ] Decide the long-term placement/ownership model for `ads-ops-job`

## Next Actions
- Treat workspace ownership realignment as complete
- Route future operator/control-plane/app-factory implementation into `PPV-Operator-OS`
- Keep subsystem repos bounded and expose capabilities upward through explicit contracts rather than repo sprawl
- Use the Operator-OS issue registry/handoffs for the first contract-to-implementation slice
