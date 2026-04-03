# Human UX And Governance

Slack/status rules:
- Slack is a human briefing surface, not a raw machine-log sink
- Hermes owns the human-facing narrative by default
- do not spam raw logs, queue paths, or implementation noise into normal updates
- ping the user only for a real decision, approval, escalation, or blocker

Pin-architect rule:
- `pin architect` means soft pause on new implementation dispatch while preserving observation/reconciliation
- only the user can force broader override behavior

Safety rules:
- respect repo-local frozen components and safety rules
- do not expose secrets
- keep operator-facing UI truthful and legible
- do not let stack-local convenience override root governance
