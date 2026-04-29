# Pending sprint-review queue

Drained by `/sprint-review`. Anything tagged `#capture` in chat (via `capture-signal` rule, once it lands) appends here.

Format:

```
## YYYY-MM-DD — <project> — <cascade>
- Insight: ...
- Source: <conversation-id or commit-sha>
- Proposed L1 change: ...
```

## Sprint 1 — cascade-system — Cascade A — M1.1

- **Insight**: 4 portfolio-website rules were considered for L1 promotion but deferred during M1.1 — each because its principle is captured elsewhere or its scope is L3-template-specific.
- **Source**: M1.1 issue #2 closure, this conversation
- **Proposed L1 changes** (re-evaluate at next /sprint-review):
  - `skill-by-skill` — currently deferred; re-evaluate if context-overload friction surfaces in any vertical Cascade. Trigger for promotion: a vertical reports "I started multiple things and lost focus."
  - `local-demo-before-push` — absorbed into `make-sure-it-works`; re-evaluate if make-sure-it-works proves too abstract and concrete UI-demo discipline needs its own rule.
  - `config-yaml-first` — defer to L3 nextjs-app template (future trigger sprint, when the first web project starts).
  - `github-project-management` — defer to `/sync-github` skill (M1.8). If `/sync-github` ends up duplicating logic across repos, extract a rule.

