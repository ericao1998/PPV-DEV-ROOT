# Org UI Design Principles

## Purpose

These principles define the default design standard for PPV internal tools, operator dashboards, admin consoles, and workflow surfaces.

The goal is not visual uniformity for its own sake. The goal is operational clarity:
- fast scanning
- obvious interaction
- trustworthy status visibility
- low ambiguity under pressure

## Core Principle

**Operational clarity comes before subtlety.**

If a human depends on the UI to inspect, triage, or control the system, the UI must optimize for clarity first and decoration second.

## Human Truth Principle

**For human operators, the UI is the source of truth.**

- backend truth that is not visible in the UI is not operational truth
- if the system knows something important, the operator surface should make it visible
- do not rely on engineers or AI assistants being able to infer hidden state from logs, helpers, or database records when the human user cannot
- if the UI and backend disagree, or the backend is correct but the UI is ambiguous, the product is still wrong from the operator's perspective

## Principle 1: Interactive Must Look Interactive

- if an element is clickable, it must visibly read as a control
- navigation items must not look like plain labels
- filters, tabs, chips, and action buttons must have explicit hover, active, and focus states
- avoid ghost-navigation patterns where clickable items visually blend into surrounding text
- avoid low-contrast pills that look decorative instead of interactive

## Principle 2: State Must Be Legible At A Glance

- healthy, warning, failed, skipped, pending, and critical states must be visually distinct
- active/selected state must be unmistakable
- status colors must be reserved for meaningful system state, not generic decoration
- the strongest problem should be easy to identify without reading the whole page

## Principle 3: Scanability Beats Spaciousness

- internal tools may be dense if they remain readable
- prioritize strong information hierarchy over oversized whitespace
- layouts should support triage, comparison, and drill-in
- the default operator view should surface the most actionable information first

## Principle 4: Navigation Must Be Structural

- use layouts that make location and movement obvious
- for internal operator tools, left-rail navigation is the default unless a stack has a strong reason otherwise
- nav items should use full-row click targets where practical
- secondary tabs/actions must also read clearly as controls

## Principle 5: Theme Variation Must Preserve Clarity

- dark and light themes may both exist
- theme switching must not change interaction clarity
- both themes must preserve contrast, state visibility, and control affordance
- dark mode must not hide controls behind low-contrast outlines
- light mode must not wash out status and action emphasis

## Principle 6: Human Source Of Truth Surfaces Need Explicit Derived State

- if the UI is the human inspection surface, it should expose derived signals that reduce operator interpretation burden
- examples:
  - health
  - review priority
  - disagreement flags
  - readiness/config state
- selected target / active record context
- permission or actionability state
- blocked vs allowed vs pending status
- do not make the operator mentally reconstruct obvious conclusions from raw fields when safe derived state can be shown directly

## Principle 7: Read-Only First Is Acceptable

- a UI may begin as read-only if the stack is not ready for write actions
- read-only does not mean low-quality
- inspection, diagnosis, and trust-building are valid first-phase UI goals
- future actions should be designed in, but not faked before the backend is ready

## Principle 8: Prefer Direct Controls For Small Action Sets

- if an operator is choosing from a small number of high-frequency actions, prefer direct buttons, segmented controls, or chips over a dropdown
- do not hide a 2-5 option operational choice behind a native select unless there is a strong reason
- for assignment, mode, or on/off decisions with low option count, the operator should be able to act with one click where practical
- native dropdowns are acceptable when option count is genuinely large, searchable, or variable enough to justify them
- if a native control feels laggy, do not keep polishing around it by default; reconsider whether it is the right control at all

## Principle 9: Heavy Operator Subviews Must Be Isolated

- do not make a dense or infrequently used operator subview ride on the global dashboard hot path if it can be loaded separately
- expensive state should be fetched only when that view is active
- forms and configuration panels should not rerender on a timer while the operator is reading or editing them
- large edit surfaces should be collapsed by default unless the expanded form is the primary task of the page
- if a page feels laggy, first ask whether the view is carrying unrelated data, polling, or DOM weight that should be isolated

## Anti-Patterns

Avoid these in PPV internal tools:
- clickable labels that do not look clickable
- low-contrast ghost tabs for important navigation
- decorative pills used as hidden nav buttons
- critical actions styled like metadata
- dashboards that require memorizing what is interactive
- status communicated only by color with no structural reinforcement
- native dropdowns for tiny high-frequency operator choices when direct controls would be clearer and faster
- configuration pages that poll and rerender while the operator is trying to edit them
- heavy subviews that piggyback on the main dashboard payload when they can be loaded separately

## Default Internal Tool Pattern

Unless a stack explicitly justifies otherwise, prefer:
- left rail navigation
- top status bar for date range, environment, and quick controls
- dense main content optimized for triage
- clearly separated primary, secondary, and tertiary controls
- read-only inspection first, controlled actions later
- direct controls for low-cardinality operational choices
- dedicated endpoints/state for heavyweight admin or configuration views

## Governance Rule

Any new internal UI, dashboard, or operator console must:
1. reference this principle document during design
2. state where it intentionally follows or departs from these rules
3. document any justified exception in the stack architecture or runbook docs
4. surface the system state a human needs to act correctly, rather than assuming hidden backend state is acceptable
