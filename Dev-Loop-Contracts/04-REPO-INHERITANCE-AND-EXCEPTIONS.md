# Repo Inheritance And Exceptions

All PPV repos inherit the root contracts from `PPV-Dev-Root`.

Repo-local docs may define:
- local architecture
- runbooks
- issue tracking
- active handoff/evidence paths
- local validation/deploy specifics

Repo-local docs may not redefine:
- the controller
- the gate owner
- the default Hermes/Codex/Claude role model
- the human-facing Slack contract
- app-factory ownership away from `PPV-Operator-OS`

Exceptions:
- temporary role shifts are allowed only when explicitly recorded in the bounded task/lane
- controller replacement requires explicit user approval
- historical docs should be marked as superseded or archival when they no longer define active behavior
