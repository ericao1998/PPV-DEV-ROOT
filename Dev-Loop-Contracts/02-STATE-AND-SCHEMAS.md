# State And Schemas

Use these distinctions consistently:
- phase = where the task is in lifecycle
- condition = what is true right now
- human status = the user-facing projection of phase + condition

Minimum task record fields:
- id
- repo
- branch
- objective
- current_phase
- current_condition
- human_status
- assigned_controller
- assigned_implementer
- assigned_reviewer
- blockers[]
- evidence[]
- updated_at

Minimum worker/review result fields:
- task_id
- agent
- role
- status
- summary
- findings[]
- validations[]
- blockers[]
- completed_at

Recommended projection rule:
- `awaiting_human` or `blocked` overrides normal phase wording in the human-facing status
