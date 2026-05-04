# Pending sprint-review queue

> **Last drained**: 2026-05-04 by `@sprint-review` against the closed Sprint 2 Vertical C milestone. Drain decisions captured in `~/Projects/cascade-system/retros/sprint-2-vertical-c-drain.md` §3. The queue-archive policy activated on this drain (entry that announced it is now resolved); archived entries live at `queue/archive/sprint-1-5.md`. Six entries were marked PROMOTE and are being landed sequentially via `@update-horizontal` / `@write-skill` (Phase 2.1 — 2.6 of the drain plan); each PROMOTE entry will be removed from this file as its PR merges.

Drained by `@sprint-review`. Anything tagged `#capture` in chat (via `capture-signal` rule, once it lands) appends here.

Format:

```
## YYYY-MM-DD — <project> — <cascade>
- Insight: ...
- Source: <conversation-id or commit-sha>
- Proposed L1 change: ...
```

## Sprint 1 — cascade-system — Cascade A — M1.1

- **Insight**: 2 portfolio-website rules remain deferred for L1 promotion (originally 4; 2 were dropped at the Sprint 2 Vertical C drain after three sprints' worth of evidence showed their principles were already covered elsewhere — see `queue/archive/sprint-1-5.md`). The remaining two await trigger-condition firing.
- **Source**: M1.1 issue #2 closure; drop decisions in `retros/sprint-2-vertical-c-drain.md` §3 (entries 1.1 + 1.2).
- **Proposed L1 changes** (re-evaluate at next @sprint-review):
  - `config-yaml-first` — defer to L3 nextjs-app template (future trigger sprint, when the first web project starts).
  - `github-project-management` — defer to `@sync-github` skill (M1.8). If `@sync-github` ends up duplicating logic across repos, extract a rule.

## Sprint 1 review — cascade-system — Cascade A — M-2 (L1 storage)

- **Insight**: All L1 components live in `~/.windsurf/` which is not a git repo. No diff history, no remote backup for skills / rules / workflows / contract / templates. Plan §3.10 left the L1 ↔ repo linkage as TBD; Sprint 1's heavy authoring (14 rules, 8 skills, 4 workflows, 1 contract, 2 templates, 12-file shared scaffold) made the gap acute.
- **Source**: `docs/reviews/sprint-1-review.md` §3.2 M-2 + `docs/decisions/ADR-008` (which notes the gate skips `~/.windsurf/` until this is resolved)
- **Proposed L1 change**: Decide a storage strategy via a focused `@grill-me` session. Candidate options:
  - (a) Init `~/.windsurf/` as its own git repo with GitHub remote (e.g., `ReebalSami/dotwindsurf`) — cleanest separation, two repos to keep in sync
  - (b) Move L1 into `cascade-system/l1/` with symlink back to `~/.windsurf/` — one repo, symlink fragility on some tools
  - (c) Sync script pattern — fragile, deferred
  - (d) Other
- **Blocker for**:
  - `@update-horizontal` — its "apply L1 changes" step is currently an unversioned file edit
  - `@sprint-review` step 8 (ADR-008 clean-tree gate) — currently skips `~/.windsurf/` with a warning; full enforcement needs the repo to exist
- **Scope note**: Sprint 2 Verticals B + C do **not** modify L1 directly, so they can proceed without this resolved. Prioritize ahead of Sprint 3 or when `@update-horizontal` gets its first real invocation.

## Sprint 1 review — cascade-system — Cascade A — queue archive policy — **RESOLVED at Sprint 2 Vertical C drain (2026-05-04)**

- **Insight**: `@sprint-review` step 10 (queue drain) says "Dropped → archive to `~/Projects/cascade-system/queue/archive/<sprint>.md` (create if missing)". The archive directory did not yet exist and no sprint had exercised the drop-to-archive path.
- **Source**: `~/.codeium/windsurf/skills/sprint-review/SKILL.md` step 10
- **Resolution**: Activated mechanically during the Sprint 2 Vertical C drain (2026-05-04). The drain produced 4 archived entries (2 dropped + 2 resolved), so `queue/archive/sprint-1-5.md` was created with full archived content per `retros/sprint-2-vertical-c-drain.md` §3. Future sprints follow the same `queue/archive/<sprint>.md` filename convention.
- **Will be removed from this queue when**: this PR (Phase 1 of the drain) merges. Retained in this drain's PR diff for traceability.

## Sprint 2 prep — cascade-system — Cascade A — brainstorm drift correction (ADR-018)

- **Insight**: The `parked-items-brainstorm.md` (commit `fa48ea2`, 2026-04-30) made two errors that ADR-018 corrected during dispatch: (1) §2.3 specified `know-your-hardware` would have a section in `global_rules.md`; (2) §3.3 specified `model_decision` activation in the global file. Both are wrong per Windsurf docs (`https://docs.windsurf.com/windsurf/cascade/memories`) — `global_rules.md` is always-on with no per-rule activation modes; the `(model_decision)` annotations there are decorative; only workspace rules support real `model_decision`. The architectural fix landed in ADR-018: `know-your-hardware` is workspace-deployed only.
- **Source**: ADR-018 §"Brainstorm drift correction"; `parked-items-brainstorm.md` §2.3 + §3.3
- **Empirical-test signal for ADR-017**: This is the brainstorm's first-dispatch artifact. The dispatch (Phase A of `~/.windsurf/plans/build-parked-items-6984b3.md`) caught the drift before it shipped — useful evidence that the brainstorm-then-dispatch pattern surfaces errors the brainstorm itself missed. Future Cascade authoring brainstorms should treat the dispatch step as a re-validation gate, not a rubber-stamp.
- **Proposed L1 change** (re-evaluate at Sprint 2 `@sprint-review`):
  - Should `@propose-extension` step 8 (route + ADR-draft presentation) include a "platform constraint check" sub-step that verifies any global_rules.md / activation-mode claims against current Windsurf docs? Possibly via a `context7` MCP query as a routine pre-flight.
  - Should the brainstorm template add a "platform-constraints checklist" section so future brainstorms catch these without dispatch-time correction?
- **Trigger for promotion**: Either at next Sprint 2 `@sprint-review`, or earlier if a similar dispatch-time correction is caught in another build.

## Sprint 2 — cascade-system — Cascade C — M2C.6 retro (`@kickoff` milestone-mode candidate)

- **Insight**: Vertical C produced 10 plan files at `~/.windsurf/plans/` for 6 milestones (`cascade-c-obsidian-kickoff-{4fa159, 52d465, 3bf063}.md`, `m2c1-obsidian-cli-execution-52d465.md`, `m2c2-{plan-verification-3bf063, vault-execution-handoff, vault-execution-kickoff-7afcc2}.md`, `cascade-c-{kickoff-m2c3-16f0dc, m2c4-priming-rule-057e79, m2c5-vault-research-skill-057e79}.md`). Each milestone after M2C.2 grew its own milestone-scoped kickoff plan rather than executing direct from the parent vertical handoff. The pattern is recurring enough across M2C.3 / M2C.4 / M2C.5 to constitute a signal rather than coincidence. `@kickoff` currently operates at the *vertical* granularity (one handoff = one orient + one focused question); mid-vertical milestone re-orientation has no first-class L1 affordance.
- **Source**: Sprint 2 Vertical C retro `retros/sprint-2-vertical-c.md` §4.1 + §7; `~/.windsurf/plans/` directory listing as of 2026-05-04.
- **Proposed L1 change** (re-evaluate at next `@sprint-review`):
  - Option A: Add a `--milestone <id>` mode to `@kickoff` that takes a milestone-scoped kickoff plan (rather than the parent vertical handoff) and orients on that milestone's AC alone, skipping the vertical-level lifecycle detection.
  - Option B: Promote mid-vertical milestone re-orientation into `/run-phase` instead — `@kickoff` stays vertical-scoped, and `/run-phase` learns to consume milestone-scoped plans within an active vertical's `phases.yaml` equivalent.
  - Option C: Accept the milestone-scoped plans as the right pattern and update `@kickoff` SKILL.md §Anti-patterns to allow them rather than discouraging them. Lowest-cost change; simply codifies what worked.
  - Decision criterion: which option minimizes the *number* of plan files per milestone without losing the orient-step value. Empirical evidence from Verticals B and D (when they run) will inform.
- **Trigger for promotion**: After Vertical D closes — three verticals' worth of milestone-plan-creation behavior is enough to decide between A / B / C. Earlier promotion if Cascade A authors a single `@update-horizontal` change that resolves all three options simultaneously.

## Sprint 2 — cascade-system — Cascade C — M2C.6 retro (design-locked-plan size threshold)

- **Insight**: M2C.2 produced a 63KB design-locked plan (`~/.windsurf/plans/cascade-c-obsidian-kickoff-3bf063.md`) that proved unwieldy in a single Cascade session's context window. The user directed a freeze + fresh-session pickup mid-design (the design phase produced the locked plan; execution then ran in a separate session via the M2C.2 execution handoff). The freeze worked — execution Phase 1–3 ran cleanly with reduced context pressure — but the freeze was reactive (forced by context-window strain) rather than proactive (planned at design start).
- **Source**: Sprint 2 Vertical C retro `retros/sprint-2-vertical-c.md` §4.3; `~/.windsurf/plans/cascade-c-obsidian-kickoff-3bf063.md` (63KB); `docs/handoffs/cascade-c-m2c2-vault-execution.md`.
- **Proposed L1 change** (re-evaluate at next `@sprint-review`):
  - Add a heuristic to `@grill-me` (or wherever design-lock plans are authored): when a plan file approaches some size threshold (~20KB candidate; empirically calibrated), surface a freeze-handoff suggestion to the user. Output shape: *"Plan is now {size}; consider a freeze-handoff to a fresh session for execution"* — single-line, suppressible, no auto-action.
  - Alternative: bake the size check into `/commit` for `~/.windsurf/plans/*` paths — surface the suggestion at commit time when a plan crosses the threshold.
  - The threshold is a *shape* not a hard count per `no-quantity-over-shape`; ~20KB is the starting hypothesis based on a single data point (the M2C.2 plan at 63KB needed freeze; smaller plans within Vertical C did not).
- **Trigger for promotion**: After Verticals B and D produce one or more design-locked plans of comparable size — three data points are enough to calibrate the threshold. If neither produces a comparable plan within Sprint 2, defer to Sprint 3.

## Sprint 2 — cascade-system — Cascade A — GA-1 (vault WRITE path / `@vault-distill` candidate)

- **Insight**: Vertical C shipped the vault's **read path** (layout per ADR-023 + access tool per ADR-022 + read-side L1 proposals queued at M2C.3-M2C.5) but did not build the **write path**. ADR-023 §"Layer ownership" says *"Cascade writes `wiki/`"* but does not say *how* or *when* — the trigger, the input scope, the distillation algorithm, the write discipline are all undefined. As a result `wiki/mocs/MOC - home.md` is a stub; no other `wiki/` content exists. The whole "Cascade-distilled durable knowledge" promise of ADR-023 §4 is unfulfilled until a write-path mechanism lands.
- **Source**: `~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md` Q2 (gap #1) + Q5 (three plausible architectures); ADR-023 §"Layer ownership" + §AGENTS.md §6 (one-canonical-home rule); Karpathy LLM-wiki gist + Cole Medin self-evolving-memory clipping (cited in gap analysis Q7); `retros/sprint-2-vertical-c-drain.md` §3 row GA-1.
- **Proposed L1 change** (re-evaluate at Sprint 3 planning OR when first vault-distill friction surfaces):
  - **Architecture A (cheapest)**: Author `@vault-distill` skill — user-invoked: *"Cascade, distill the N clippings in `raw/_inbox/<topic>/` into `wiki/concepts/<domain>/`."* Explicit, auditable, low-risk. Bookends with `@vault-research` (M2C.5) for symmetry: research = read; distill = write.
  - **Architecture B (Karpathy-style scheduled flush)**: Cron/hook runs daily, takes new `raw/` + `originals/` entries and auto-extracts into `wiki/`. Requires Windsurf session hooks we don't have (see GA-3) or external cron. Higher infrastructure cost.
  - **Architecture C (conversation-triggered)**: At end of `@grill-me` or `@to-prd` session, ask *"Should findings X, Y, Z be written to `wiki/concepts/<topic>`?"* — couples knowledge capture to the work that produced it. Most intellectually honest (write-only-what-you-learned).
  - User's gap-analysis recommendation: **Architecture A** for v1 (cheapest), **C** as evolution path. **B** deferred until Windsurf gains session hooks.
- **Pairs with**: queue entry §"M-2 (L1 storage)" — both are Sprint 3 strategic-vertical candidates. Bundle into one `@grill-me` session.
- **Severity**: High (gap analysis Q2 row #1). The unfulfilled half of ADR-023's promise.
- **Trigger for promotion**: Sprint 3 planning OR when the user first reaches for `wiki/` content and finds none + says *"why is the wiki empty?"*.

## Sprint 2 — cascade-system — Cascade A — GA-2 (ADR ↔ vault relationship — future ADR proposal)

- **Insight**: cascade-system's own knowledge (ADRs at `docs/decisions/`, retros at `retros/`, queue at `queue/`) lives in the meta-repo and has no defined relationship to the vault. ADR-023 scoped this out per handoff §4 privacy guideline. The decision is currently *"separation by default"* (Posture A from gap analysis Q6) but is undocumented. As `wiki/` accumulates content, two independent knowledge graphs will drift.
- **Source**: `~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md` Q6 (three postures: A separation / B unified discovery via vault sources cards / C vault-first); `retros/sprint-2-vertical-c-drain.md` §3 row GA-2.
- **Three postures** (gap analysis Q6, verbatim shape):
  - **Posture A — Separation (current default)**: cascade-system `docs/decisions/` stays canonical. Vault never sees ADRs. Two audiences, two homes. Simplest. Works fine until vault has content.
  - **Posture B — Unified discovery**: ADR files stay at `docs/decisions/`. Vault adds `wiki/sources/adrs/<slug>.md` cards with 1-line summary + `[[wikilinks]]` + path back to canonical file. One-canonical-home rule preserved.
  - **Posture C — Vault-first**: ADRs authored inside vault, rendered/exported to `docs/decisions/` for git history. Treats vault as source of truth. Highest tooling cost.
- **Gap-analysis weak recommendation**: **B** if/when vault has enough `wiki/` content to make cross-linking useful. **A** acceptable until then.
- **Proposed L1 change** (re-evaluate when trigger fires): Author **ADR-NNN** declaring the chosen posture. Posture A = no further action. Posture B = small `wiki/sources/adrs/` retro-fill pass + `@docs-refresh` enhancement to maintain ADR↔vault cards. Posture C = bigger lift; defer.
- **Severity**: Medium (gap analysis Q2 row #2). Latent gap; becomes noise as `wiki/` fills.
- **Trigger for promotion**: When `wiki/` accumulates ≥5 cards (signals real content; cross-linking becomes useful) OR when a future ADR struggles to surface to the user via `docs/decisions/` only.

## Sprint 2 — cascade-system — Cascade A — GA-3 (Windsurf session-hooks equivalent — Cole Medin pattern)

- **Insight**: Cole Medin's "self-evolving memory" pattern (cited in gap analysis Q7) auto-captures every Claude Code session into `daily logs/` via `SessionStart` / `PreCompact` / `SessionEnd` hooks. Windsurf has rules (`always_on` / `model_decision`) but no session-boundary callbacks. As a result, `bidirectional-learning-pipe` is manually triggered (user says *"capture this"*) — works but requires user discipline. An automated path would surface insights/friction Cascade observes inline that the user might not flag.
- **Source**: `~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md` Q7 (Karpathy vs Cole Medin scope disambiguation); `retros/sprint-2-vertical-c-drain.md` §3 row GA-3; `bidirectional-learning-pipe` rule (current closest analog).
- **Proposed L1 change** (deferred — blocked by Windsurf platform):
  - **If Windsurf adds session hooks**: author `~/.codeium/windsurf/skills/session-capture/SKILL.md` that hooks `SessionEnd` to scan transcript for `#capture` tags + auto-append to `queue/pending-review.md` (project-local) or vault `raw/_inbox/sessions/<date>.md` (cross-project). Mirror Cole Medin's `daily logs/` pattern but vault-resident per ADR-023.
  - **If Windsurf does not add session hooks**: keep manual capture. Reinforce `bidirectional-learning-pipe` discipline in `@sprint-review` step 4 (draft retro) by explicitly checking *"any insight Cascade should have captured but didn't?"* per session walked.
- **Severity**: Low (gap analysis Q2 row #3). Manual capture is working; automation is nice-to-have. Blocked by external platform decision.
- **Trigger for promotion**: When Windsurf ships session-boundary callbacks OR when `bidirectional-learning-pipe` shows manual-capture failure rate > some threshold (no concrete metric yet; would need a retro to surface "we missed N insights this sprint" pattern).

---
