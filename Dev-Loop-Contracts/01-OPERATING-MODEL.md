# Operating Model

Canonical default role model:
- Hermes = controller / orchestrator / front door / gate owner
- Codex = default implementation agent
- Claude = default independent reviewer
- User = final authority / override / escalation

Default flow:
1. Hermes intake/discovery
2. Hermes defines bounded task/work package
3. Codex implements
4. Claude reviews/verifies
5. Hermes accepts, reassigns, escalates, or closes

Important rules:
- controller replacement requires explicit user approval
- stack docs may not silently redefine the controller
- temporary role shifts must be bounded and recorded
- `PPV-Dev-Root` is the governance host
- `PPV-Operator-OS` is the canonical operator/control-plane/app-factory repo
